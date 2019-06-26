---
title: react-native-打包
date: 2019-06-26
tags: ['React Native']
categories: React Native
---

### Android和iOS打包
<!-- more -->

#### Android

```
1.生成签名 - 生成一个有效期10000天的证书，证书为： my-release-key.keystore
注意: 有效期写长，密码要记住
keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

2.配置打包时的签名-android/app/gradle.properties
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_KEY_PASSWORD=123456
MYAPP_RELEASE_STORE_PASSWORD=123456
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
android.useDeprecatedNdk=true

3.添加签名 - android/app/build.gradle
android {
  ...
  defaultConfig { ... }
  signingConfigs {
      release {
          storeFile file(MYAPP_RELEASE_STORE_FILE)
          storePassword MYAPP_RELEASE_STORE_PASSWORD
          keyAlias MYAPP_RELEASE_KEY_ALIAS
          keyPassword MYAPP_RELEASE_KEY_PASSWORD
      }
  }
  buildTypes {
      release {
          ...
          signingConfig signingConfigs.release
      }
  }
}

4.生成apk
生成的apk文件在， android/app/build/outputs/apk/ 下
./gradlew assembleRelease
```

#### iOS

```
1.找到ios目录，在该目录下新建bundle目录

2.package.json
{
    "scripts":{
        "bundle-ios":"node node_modules/react-native/local-cli/cli.js bundle --entry-file index.js  --platform ios --dev false --bundle-output ./ios/bundle/index.ios.jsbundle --assets-dest ./ios/bundle"
    }
}

3.运行npm run bundle-ios

4.Xcode中集成，添加资源到项目中，必须选择Create folder references的方式添加文件夹

5.添加证书、配置描述文件打包
```
