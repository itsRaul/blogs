---
title: Flutter学习之路 - Flutter 开发环境安装
date: 2020-05-23 10:12:13
tags: ['Flutter']
categories: Flutter
---

### Mac 电脑 Flutter 开发环境

<!-- more -->

### 环境变量设置

1.打开环境变量设置文件 open .bash_profile

Flutter 官方为中国开发者搭建了临时镜像，加入环境变量

```
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

2.下载 Flutter SDK(选择稳定版)

解压 SDK 到一个你知道到目录下，加入环境变量,复杂 Flutter SDK 下到 bin 目录
例如： SDK 在 /Users/用户/Documents/flutter/bin

```
export PATH=`pwd`/flutter/bin:$PATH

SDK的目录
export PATH= /Users/用户/Documents/flutter/bin :$PATH
```

3.运行 flutter doctor 命令查看是否需要安装其它依赖项来完成安装

4.创建项目

```
flutter create my_app
```

### iOS 设置

1.安装 Xcode，根据 flutter 的 SDK 版本要求下载不同的 Xcode 版本

2.运行 open -a Simulator 打开模拟器

3.运行 flutter run 启动您的应用

### Android 设置

1.安装 Android Studio

2.环境变量

```
#Android 环境变量
export ANDROID_HOME=/Users/你的用户名/Library/Android/sdk
#Android 模拟器路径
export PATH=${PATH}:${ANDROID_HOME}/emulator
#Android tools 路径
export PATH=${PATH}:${ANDROID_HOME}/tools
#Android 平台工具路径
export PATH=${PATH}:${ANDROID_HOME}/platform-tools
#Android NDK路径
ANDROID_NDK_HOME=/Users/你的用户名/Library/Android/ndk/android-ndk-r10e
```

3.插件安装
打开 Preferences > Plugins (macOS), File > Settings > Plugins (Windows & Linux)
安装 Flutter、Dart 插件

启动项目不同版本的 Android Studio 的 SDK 要求不一样，具体看是否报错

### VScode 编辑器设置

1.command + shift + p ,Flutter: New Project 也可以创建 Flutter 项目

2.下载 Dart 和 Flutter 扩展

3.打开项目，右下角 No Device 选择模拟器

4.command + shift + p 选择 Debug: Start Debugging 启动项目

### 总结

Flutter 开发环境比 RN 的开发环境安装要简单的多，按照文档安装即可
