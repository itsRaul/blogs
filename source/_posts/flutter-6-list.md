---
title: Flutter学习之路 - 混合开发
date: 2020-08-11 17:16:19
tags: ['Flutter']
categories: Flutter
---

### 混合开发

<!-- more -->

### Flutter 集成步骤

- 创建 Flutter module

```
flutter create -t module flutter_module
```

- 添加 Flutter module 依赖
- 在 Java/Object-c 中调用 Flutter module

### Android

#### 引入 Flutter module

1. 打开 settings.gradle(Project Settings)

```
自动会关联到一个Flutter的模块
setBinding(new Binding([gradle: this]))                                 // new
evaluate(new File(                                                      // new
    settingsDir.parentFile,                                                // new
    'flutter_module/.android/include_flutter.groovy'// new
))

'flutter_module/.android/include_flutter.groovy'
flutter_module的Flutter项目和android项目在同一文件夹下
```

2. 打开 build.gradle(Module:app)

```
minSdkVersion 16才支持Fluuter module
defaultConfig{
  minSdkVersion 16
}
```

```
android最下面加入以下代码，进行编译
android {
  ...
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

```
dependencies {
  ...
  implementation project(':flutter')
}
```

3. 问题：如果出现“程序包 android.support.annotation 不存在”的错误，需要使用如下的命令来创建 Flutter 模块，因为最新版本的 Android 默认使用 androidx 来管理包。

```
flutter create --androidx -t module flutter_library

如果这个命令无法找到，升级SDK
```

#### Java 调用 Flutter module

- Flutter.createView

```
public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    View flutterView = Flutter.createView(this, getLifecycle(), "route1");
    FrameLayout.LayoutParams layoutParams = new FrameLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
    addContentView(flutterView, layoutParams);
  }
}
```

- FlutterFragment

```
public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    FragmentTransaction ft= getSupportFragmentManager().beginTransaction();
    ft.replace(R.id.fragment_container, Flutter.createFragment("Hello Flutter"));
    ft.commit();
  }
}
```

#### Flutter 接受参数

```
import 'dart:ui';

void main => runApp(MyApp(initParams: window.defaultRouteName))

class MyApp extends StatelessWidget {
  //接受initParams参数
  final String initParams

  const MyApp({Key key, this.initParams}) : super(key:key);
}

```

### iOS

#### iOS 集成 Flutter

- 在 ios 项目下 pod init

```
Podfile

# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'
target 'FlutterHybridiOS' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for FlutterHybridiOS

  //flutter项目的路径
  flutter_application_path = '../flutter_module/'

  eval(File.read(File.join(flutter_application_path,'.ios', 'Flutter', 'podhelper.rb')),binding)

  target 'FlutterHybridiOSTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'FlutterHybridiOSUITests' do
    inherit! :search_paths
    # Pods for testing
  end

end

```

- pod install

- FlutterHybridiOS.xcworkspace 打开 iOS 项目

- 【TAGETS】→【Build Setttings】→【Build Options】→【Enable Bitcode】来禁用 Bitcode

- 【Enable Phases】，然后点击左上角的加号新建一个“New Run Script Phase”，添加如下脚本代码。

```
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
```

- build 项目成功即完成

#### Object-c 调用 Flutter module

```
ViewController.m

#import <Flutter/Flutter.h>
#import "AppDelegate.h"
#import "ViewController.h"
#import <FlutterPluginRegistrant/GeneratedPluginRegistrant.h>

@interface ViewController ()

@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    [button addTarget:self
               action:@selector(handleButtonAction)
     forControlEvents:UIControlEventTouchUpInside];
    [button setTitle:@"Test" forState:UIControlStateNormal];
    [button setBackgroundColor:[UIColor redColor]];
    button.frame = CGRectMake(80.0,210.0,160.0,40.0);
    [self.view addSubview:button];
}

- (void)handleButtonAction {
    //以一个完整页面打开Flutter模块
    FlutterViewController *flutterViewController = [FlutterViewController new];

    [flutterViewController setInitialRoute:@"{name:'devio',dataList:['aa','bb',''cc]}"];

    [self presentViewController:flutterViewController animated:true completion:nil];
}
@end
```

### Flutter 和 H5 混合开发

```
flutter_webview_plugin: ^0.3.11


import 'dart:async';

import 'package:flutter/material.dart';
import 'package:flutter_webview_plugin/flutter_webview_plugin.dart';

//白名单
const CATCH_URLS = ['m.ctrip.com/', 'm.ctrip.com/html5/', 'm.ctrip.com/html5', 'dp.ctrip.com/'];

class Webview extends StatefulWidget {
  final String url;
  final String statusBarColor;
  final String title;
  final bool hideAppBar;
  final bool backForbid;


  Webview({Key key, this.url , this.statusBarColor, this.title, this.hideAppBar, this.backForbid=false});

  @override
  _WebviewState createState() => _WebviewState();
}

class _WebviewState extends State<Webview>{
  final webviewReference = FlutterWebviewPlugin();
  StreamSubscription<String> _onUrlChanged;
  StreamSubscription<WebViewStateChanged> _onStateChanged;
  StreamSubscription<WebViewHttpError> _onHttpError;
  bool exiting = false;
  @override
  void initState() {
    super.initState();
    //防止页面重新打开
    webviewReference.close();
    //注册监听，页面url发生变化进行监听，返回String
    _onUrlChanged = webviewReference.onUrlChanged.listen((String url) {});
    //注册页面状态发生变化
    //startLoad开始加载的时候
    _onStateChanged = webviewReference.onStateChanged.listen((WebViewStateChanged state) {
      switch (state.type) {
        case WebViewState.startLoad:
          if (_isToMain(state.url) && !exiting) {
            if (widget.backForbid) {
              webviewReference.launch(widget.url);
            }else {
              Navigator.pop(context);
              exiting = true;
            }
          }
          break;
        default:
          break;
      }
    });
    //url发生错误
    _onHttpError = webviewReference.onHttpError.listen((WebViewHttpError error) {
      print(error);
    });
  }

  @override
  void dispose() {
    super.dispose();
    //页面关闭将对应的监听取消
    _onUrlChanged.cancel();
    _onStateChanged.cancel();
    _onHttpError.cancel();
    webviewReference.dispose();
  }

  //判断是否在白名单，在的话返回Flutter的上一个页面，不在不做任何处理
  _isToMain(String url) {
    bool contain = false;
    for (final value in CATCH_URLS) {
      if (url?.endsWith(value)??false) {
        contain = true;
        break;
      }
    }
    return contain;
  }

  @override
  Widget build(BuildContext context) {
    String statusBarColorStr = widget.statusBarColor ?? 'ffffff';
    Color backButtonColor;
    if (statusBarColorStr == 'ffffff') {
      backButtonColor = Colors.black;
    } else {
      backButtonColor = Colors.white;
    }
    return Scaffold(
      body: Column(
        children: <Widget>[
          _appBar(Color(int.parse('0xff'+statusBarColorStr)),backButtonColor),
          Expanded(
            child: WebviewScaffold(
              url: widget.url,
              withZoom: true,
              hidden: true,
              initialChild: Container(
                color: Colors.white,
                child: Center(
                  child: Text('Waiting.....'),
                )
              ),
            ),
          )
        ],
      ),
    );
  }

  _appBar(Color backgroundColor, Color backButtonColor) {
    if (widget.hideAppBar??false) {
      return Container(
        color: backgroundColor,
        height: 30,
      );
    }
    return Container(
      child: FractionallySizedBox(
        child:  Stack(
          children: <Widget>[
            GestureDetector(
              child: Container(
                margin: EdgeInsets.only(left:10),
                child: Icon(
                  Icons.close,
                  color: backButtonColor,
                  size: 26,
                )
              ),
            ),
            Positioned(
              left: 0,
              right: 0,
              child: Center(
                child: Text(
                  widget.title,
                  style: TextStyle(color:backgroundColor,fontSize:20),
                ),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```
