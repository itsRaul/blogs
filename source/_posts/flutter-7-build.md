---
title: Flutter学习之路 - 适配和打包
date: 2020-08-11 17:25:47
tags: ['Flutter']
categories: Flutter
---

### 适配和打包

<!-- more -->

### 解决启动白屏

- flutter 启动应用，初始化 SDK，flutter 的代码加载到内存里面，完成渲染，在这个加载过程中，没有内容显示，所以会出现白屏。

- 分别为 android 和 ios 启动屏添加启动屏图片

### 全面屏适配

1. SafeArea 将组件进行包裹即可

2. MediaQuery.of(context).padding 计算顶部和底部的 padding 值

```
final EdgeInsets paddings = MediaQuery.of(context).padding;
```

```
android

AndroidManifest.xml

<!--适配全面屏-->
<meta-data
  android:name="android.max_aspect"
  android:value="2.3" />
```

### 项目优化

- 代码优化

- 包大小

  压缩本地图片

  使用单架构 so

- 流畅性优化

- 内存优化

### Android 打包

- AndroidMainifest.xml

```
添加APP名：android:label
<application
  android:name="io.flutter.app.FlutterApplication"
  android:label="FlutterDemo"
  android:icon="@mipmap/ic_launcher">
```

main.dart 文件的 title 进行修改

- 检查和配置 build.gradle(Module:app) 文件

```
android: {
  compileSdkVersion: 28 //兼容到android28

  defaultConfig: {
    applicationId ''  //包名
    minSdkVersion 16 //兼容到android最低版本16,也就是安卓4.1
    targetSdkVersion 28
    versionCode 1 //版本
    versionName: '1.0.0' //版本号
  }
}
```

- 启动图标

res-mipmap-ic_launcher

- APP 签名

androidStudio 的 build - Generate Signed Bundle / APK

选择 APK - create new

创建一个名为 <app dir>/android/key.properties 的文件，它包含了密钥库位置的定义.填写创建签名的信息

在 gradle 中配置签名

```
通过编辑 <app dir>/android/app/build.gradle 文件来为我们的 app 配置签名：
在 android 代码块之前添加：

def keystoreProperties = new Properties()
def keystorePropertiesFile = rootProject.file('key.properties')
if (keystorePropertiesFile.exists()) {
    keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
}

android {
      ...
}


在 buildTypes 代码块之前添加：

 signingConfigs {
    release {
      keyAlias keystoreProperties['keyAlias']
      keyPassword keystoreProperties['keyPassword']
      storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
      storePassword keystoreProperties['storePassword']
    }
}
buildTypes {
  release {
    signingConfig signingConfigs.release
  }
}
```

启用混淆器，具体看官网

flutter build apk

### 升级

- 更新 Flutter SDK 和 packages

```
根目录运行
flutter upgrade
```

- 只更新包

```
手动式-插件官网

命令式
flutter packages upgrade
flutter packages get
```
