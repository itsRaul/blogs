---
title: Flutter学习之路 - Flutter 基础知识
date: 2020-05-27 09:54:52
tags: ['Flutter']
categories: Flutter
---

### Flutter基础知识

<!-- more -->

### 如何使用 Flutter 包和插件

1.插件地址: https://pub.flutter-io.cn/

2.搜索插件,查看 Readme

### 组件

1.StatelessWidget
StatelessWidget 不依赖自身的状态来重新渲染自己

2.StatefulWidget 
StatefulWidget 是可变状态的 widget。 使用 setState 方法管理 StatefulWidget 的状态的改变。调用 setState 告诉 Flutter 框架，某个状态发生了变化，Flutter 会重新运行 build 方法，以便应用程序可以应用最新状态。

3.Scaffold
Scaffold定义一个UI框架，框架里面包含了appBar、bottomNavigationBar、floatingActionButton、body等

4.Scaffold下的appBar

页面的导航栏

```
home: Scaffold(
   appBar: AppBar(title: Text('Flutter')),
)
```

5.Scaffold下的bottomNavigationBar(底部导航栏配置)

```
bottomNavigationBar: BottomNavigationBar(
  currentIndex: _currentIndex, //定义索引值
  onTap: (index) {
    setState(() {
      _currentIndex = index;
    });
  },//点击底部导航栏
  items: [
    BottomNavigationBarItem(
      icon: Icon(Icons.home,color: Colors.grey),
      activeIcon: Icon(Icons.home, color: Colors.blue),
      title: Text('首页')
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.list,color: Colors.grey),
      activeIcon: Icon(Icons.list, color: Colors.blue),
      title: Text('列表')
    )
  ]
),
```
6.Scaffold下的floatingActionButton(悬浮按钮)

```
floatingActionButton: FloatingActionButton(
  onPressed: null,
  child: Text('点我')
),
```

### 使用Flutter的路由与导航

1.注册路由

```
@override
Widget build(BuildContext context) {
  return MaterialApp(
    routes: <String,WidgetBuilder> {
      'less': (BuildContext context) => FlutterLaoutPage()
    },
  );
}
```

2.跳转页面

```
Navigator.pushNamed(context, 'less');
```
or
```
Navigator.push(context, MaterialPageRoute(builder: (context) => FlutterLaoutPage()));
```

### 检测用户手势以及处理点击事件

1.GestureDetector
一个用于手势识别的功能性组件，我们通过它可以来识别各种手势

2.事件

```
 body: FractionallySizedBox(
  widthFactor: 1,
  child: Stack(
    children: <Widget>[
      GestureDetector(
        onTap: () => _pringMsg('点击'),
        onDoubleTap: () => _pringMsg('双击'),
        onLongPress: () => _pringMsg('长按'),
        onTapCancel: () => _pringMsg('取消'),
        onTapUp: (e) => _pringMsg('松开'),
        onTapDown: (e) => _pringMsg('按下'),
        child: Container(
          padding: EdgeInsets.all(60),
          decoration: BoxDecoration(color: Colors.blueAccent),
          child: Text(
            '点我',
            style: TextStyle(
              fontSize: 36,
              color: Colors.white
            )
          ),
        )
      ),
      Text(printString),
      Positioned(
        left: moveX,
        top: moveY,
        child: GestureDetector(
          onPanUpdate: (e) => _doMove(e),
          child: Container(
            width: 72,
            height: 72,
            decoration:BoxDecoration(
              color:Colors.amber,
              borderRadius: BorderRadius.circular(36)
            ),
          ),
        ),
      )
    ],
  ),
),

_pringMsg(String msg) {
  setState(() {
    printString += ' , $msg';
  });
}

_doMove(DragUpdateDetails e) {
  setState(() {
    moveY += e.delta.dy;
    moveX += e.delta.dx;
  });
}
```

### 打开第三方应用

```
url_launcher: ^5.0.2

//打开网页
_launchURL() async {
  const url = 'http://www.liufupeng.cn/';
  if (await canLaunch(url)) {
    await launch(url);
  } else {
    throw 'Could not launch $url';
  }
}

//打开本地地图
_openMap() async {
  //android
  const url = 'geo:52.32.4.917'; //APP提供者的 schema
  if (await canLaunch(url)) {
    await launch(url);
  } else {
    //ios
    const url = 'http://maps.apple.com/?ll=geo:52.32.4.917';
    if (await canLaunch(url)) {
        await launch(url);
    }else {
      throw 'Could not launch $url';
    }
  }
}
```

### 生命周期

1.无状态的StatelessWidget只有createElement、与build两个生命周期方法

2.StatefulWidget的生命周期分为三组

初始化时期
createState、initState

更新时期
didChangeDependencies、build、didUpdateWidget

销毁期
deactivate、dispose

```
import 'package:flutter/material.dart';

class LfiecycleState extends StatefulWidget {
  @override
  _LfiecycleState createState() => _LfiecycleState();
}

class _LfiecycleState extends State<LfiecycleState> {

  int _count = 0;

  // 在这个方法中通常会做一些初始化工作，如channel的初始化，监听器的初始化
  @override
  void initState() {
    print('----initState----');
    super.initState();
  }

  //当依赖的State对象改变时会调用
  //在第一次构建widget时，会initState()之后立即调用此方法
  //如果的StatefulWidget依赖于InheritedWidget，那么当当前State会依赖InheritedWidget
  //InheritedWidget可以高效的将数据在Widget树中向下传递、共享
  @override
  void didChangeDependencies() {
    print('----didChangeDependencies----');
    super.didChangeDependencies();
  }

  //这是一个必须实现的方法，在这里会实现你要呈现的页面内容
  //会在didChangeDependencies()之后立即调用
  //另外调用setState后也会再次调用该方法
  @override
  Widget build(BuildContext context) {
    print('----build----');
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('生命周期')
        ),
        body: Center(
          child: Column(
            children: <Widget>[
              RaisedButton(
                onPressed: () {
                  setState(() {
                    _count +=1;
                  });
                },
                child: Text('点我'),
              ),
              Text(_count.toString())
            ]
          ),
        ),
      )
    );
  }

  //不常用的生命周期方法，当父组件需要重新绘制时才会调用
  //该方法携带一个oldWidget参数，可以将其与当前当widget进行对比以便执行一些额外的逻辑
  // if (oldWidget.x != widget.x) {}
  @override
  void didUpdateWidget(LfiecycleState oldWidget) { 
    print('----didUpdateWidget----');
    super.didUpdateWidget(oldWidget);
  }

  @override
  void deactivate() { 
    print('----deactivate----');
    super.deactivate();
  }

  @override
  void dispose() { 
    print('----dispose----');
    super.dispose();
  }
}
```

