---
title: react-native常用组件
date: 2019-07-03
tags: ['React Native']
categories: React Native
---

### react-native常用组件
<!-- more -->

#### 底部导航菜单 - react-native-tab-navigator
props={this.props} 把react-navigation的api传递到各组件

```
npm install react-native-tab-navigator --save

import TabNavigator from 'react-native-tab-navigator';
<View style={styles.container}>
  <TabNavigator>
    <TabNavigator.Item
      selected={this.state.selectedTab === 'home'}
      selectedTitleStyle={{color:'red'}} //文字的颜色
      title="首页"
      renderIcon={() => <Image style={styles.image} source={require('../assets/images/ic_polular.png')} />}
      renderSelectedIcon={() => <Image style={[styles.image,{tintColor:'red'}]} source={require('../assets/images/ic_polular.png')} />}
      onPress={() => this.setState({ selectedTab: 'home' })}>
      <Home props={this.props}/>
    </TabNavigator.Item>
    <TabNavigator.Item
      selected={this.state.selectedTab === 'my'}
      selectedTitleStyle={{color:'red'}}
      title="我的"
      renderIcon={() => <Image style={styles.image} source={require('../assets/images/ic_my.png')} />}
      renderSelectedIcon={() => <Image style={[styles.image,{tintColor:'red'}]} source={require('../assets/images/ic_my.png')} />}
      onPress={() => this.setState({ selectedTab: 'my' })}>
      <My props={this.props}/>
    </TabNavigator.Item>
  </TabNavigator>
</View>
```

#### 顶部导航栏 - react-native-scrollable-tab-view 

```
npm install react-native-scrollable-tab-view --save
ScrollableTabBar: 自定义

import ScrollableTabView, {ScrollableTabBar} from 'react-native-scrollable-tab-view'
<ScrollableTabView
  tabBarUnderlineStyle={{backgroundColor: '#e7e7e7', height: 2}}
  tabBarInactiveTextColor='mintcream'
  tabBarActiveTextColor='white'
  ref="scrollableTabView"
  tabBarBackgroundColor={'#2196f3'}
  initialPage={0}
  renderTabBar={()=><ScrollableTabBar style={{height: 40, borderWidth: 0, elevation: 2}}  tabStyle={{height: 39}} />}
>
  <PopularTab tabLabel="Java"></PopularTab>
  <PopularTab tabLabel="iOS"></PopularTab>
</ScrollableTabView>
```
#### 复选框 - react-native-check-box

```
npm install react-native-check-box --save

import CheckBox from 'react-native-check-box'
<CheckBox
  style={{flex: 1, padding: 10}}   //整行的checkBox
  onClick={() => this.onClick(data, index)} //复选框的点击事件
  isChecked={data.checked} //复选框的状态
  leftText={data.name}  //复选框的name
  checkedImage={this._checkedImage(true)} //选中
  unCheckedImage={this._checkedImage(false)} //未选中
/>

//复选框的是否选中
onClick(data, index) {
  data.checked = !data.checked;
  ArrayUtil.updateArray(this.changeValues, data); //更新数组的工具函数
  this.state.keys[index] = data;//更新state以便显示选中状态
  this.setState({
      keys: this.state.keys
  })
}

/**
  * 判断复选框的视图
  * @param {Boolean} checked 
  */
_checkedImage(checked) {
  return <Ionicons
    name={checked ? 'ios-checkbox' : 'md-square-outline'}
    size={20}
    style={{
        color: '#2196f3',
    }}/>
}
```

#### 渲染HTML文本 - react-native-htmlview

```
<HTMLView
  value={description} //HTML文本内容
  onLinkPress={(url) => {
  }} //渲染的文本有链接的时候，单击链接会回调这个方法
  stylesheet={{
      p: styles.description,
      a: styles.description,
  }} //指定标签的样式
 /> 
```

#### Modal - 弹框组件

```
/**
 *弹框组件
 */
import React, {Component} from 'react'
import {Modal, Text, TouchableOpacity, StyleSheet, View, Platform, DeviceInfo} from 'react-native'
import MaterialIcons from 'react-native-vector-icons/MaterialIcons'

export default class TrendingDialog extends Component {
    state = {
        visible: false,
    };

    show() {
      this.setState({
        visible: true,
      })
    }

    dismiss() {
      this.setState({
        visible: false,
      })
    }

    render() {
        const {onClose, onSelect} = this.props;
        return (
          <Modal
            transparent={true}
            visible={this.state.visible}
            onRequestClose={() => onClose}
          >
            <TouchableOpacity
              style={styles.container}
              onPress={() => this.dismiss()}
            >
              <MaterialIcons
                name={'arrow-drop-up'}
                size={36}
                style={styles.arrow}
              />
              <View style={styles.content}>
                <TouchableOpacity onPress={() => onSelect('11')}>
                  <View style={styles.text_container}>
                    <Text style={styles.text}>Displaying the popover on top of other elements</Text>
                  </View>
                </TouchableOpacity>
                <TouchableOpacity onPress={() => onSelect('11')}>
                  <View style={styles.text_container}>
                    <Text style={styles.text}>Displaying the popover on top of other elements</Text>
                  </View>
                </TouchableOpacity>
              </View>
              </TouchableOpacity>
            </Modal>

        )
    }
}
const styles = StyleSheet.create({
    container: {
      backgroundColor: 'rgba(0,0,0,0.6)',
      flex: 1,
      alignItems: 'center',
      paddingTop: DeviceInfo.isIPhoneX_deprecated ? 30 : 0
    },
    arrow: {
      marginTop: 40,
      color: 'white',
      padding: 0,
      margin: -15
    },
    content: {
      backgroundColor: 'white',
      borderRadius: 3,
      paddingTop: 3,
      paddingBottom: 3,
      marginRight: 3,
    },
    text_container: {
      alignItems: 'center',
      flexDirection: 'row',
      justifyContent: 'center'
    },
    text: {
      fontSize: 16,
      color: 'black',
      fontWeight: '400',
      padding: 8,
      paddingLeft: 26,
      paddingRight: 26
    },
    line: {
      height: 0.3,
      backgroundColor: 'darkgray',
    },
});
```

```
使用

<Button title='弹出' onPress={() => this.dialog.show()/>
<Button title='关闭' onPress={() => this.dialog.dismiss()/>

renderTrendingDialog() {
  return <TrendingDialog
      ref={dialog => this.dialog = dialog}
      onSelect={tab => this.onSelectTimeSpan(tab)}
  />
}
```

#### 本地存储 - react-native-storage

```
npm install react-native-storage
npm install @react-native-community/async-storage
react-native link @react-native-community/async-storage

import Storage from 'react-native-storage';
import AsyncStorage from '@react-native-community/async-storage';

const STORAGE = new Storage({
  // 最大容量，默认值1000条数据循环存储
  size: 100,

  // 存储引擎：对于RN使用AsyncStorage，对于web使用window.localStorage
  // 如果不指定则数据只会保存在内存中，重启后即丢失
  storageBackend: AsyncStorage,

  // 数据过期时间，默认一整天（1000 * 3600 * 24 毫秒），设为null则永不过期
  defaultExpires: null,

  // 读写时在内存中缓存数据。默认启用。
  enableCache: true, // 你可以在构造函数这里就写好sync的方法 // 或是在任何时候，直接对storage.sync进行赋值修改 // 或是写到另一个文件里，这里require引入

  // 如果storage中没有相应数据，或数据已过期，
  // 则会调用相应的sync方法，无缝返回最新数据。
  // sync方法的具体说明会在后文提到
  //sync: require('你可以另外写一个文件专门处理sync'),
});

global.STORAGE = STORAGE
```

```
使用：

onSave() {
  STORAGE.save({
    key: 'loginState',
    data: {
      from: 'some other site',
      userid: 'some userid',
      token: 'some token',
    }
  })
}
onLoad() {
  STORAGE.load({
    key: 'loginState'
  }).then(ret => {
    console.log(ret)
  }).catch(err => {
    console.warn(err.message)
  })
}
onRemove() {
  STORAGE.remove({
    key: 'loginState',
  });
}
```

#### 轻提示 - react-native-easy-toast

```
npm i react-native-easy-toast --save

import Toast, {DURATION} from 'react-native-easy-toast'
<Toast
  ref={toast => this.toast = toast}
  position={'center'}
/>

//使用
this.toast.show('hello')
```

#### 类似滚动视图的组件，支持视差和粘性头 - react-native-parallax-scroll-view

```
npm install react-native-parallax-scroll-view --save

import ParallaxScrollView from 'react-native-parallax-scroll-view'

// 配置
getParallaxRenderConfig(params) {
  let config = {}
  //视差头的背景，封面图像
  config.renderBackground = () => (
    <View key="background">
      <Image source={{
        uri: params.backgroundImg,
        width: window.width,
        height: PARALLAX_HEADER_HEIGHT
      }}/>
      <View style={{
        position: 'absolute',
        top: 0,
        width: window.width,
        backgroundColor: 'rgba(0,0,0,.4)',
        height: PARALLAX_HEADER_HEIGHT
      }}/>
    </View>
  );
  // 背景显示的内容
  config.renderForeground = () => (
    <View key="parallax-header" style={styles.parallaxHeader}>
      <Image style={styles.avatar} source={{url:params.avatar}}/>
        <Text style={ styles.sectionSpeakerText }>
            {params.name}
        </Text>
        <Text style={ styles.sectionTitleText }>
            {params.description}
        </Text>
    </View>
  );
  //当视差标题向上滚动时，它会粘在视图的顶部。
  config.renderStickyHeader = () => (
    <View key="sticky-header" style={styles.stickySection}>
      <Text style={styles.stickySectionText}>{params.name}</Text>
    </View>
  );
  //固定在视图顶部
  config.renderFixedHeader = () => (
    <View key="fixed-header" style={styles.fixedSection}>
      {ViewUtil.getLeftBackButton(()=> NavigationUtil.goBack(this.props.navigation))}
      {ViewUtil.getShareButton(()=>this.onShare())}
    </View>
  ); 
  return config
}

renderView(content,params) {
  let renderConfig = this.getParallaxRenderConfig(params)
  return (
    <ParallaxScrollView
      backgroundColor="#fff"
      contentBackgroundColor="#f3f3f4"
      parallaxHeaderHeight={PARALLAX_HEADER_HEIGHT}
      stickyHeaderHeight={STICKY_HEADER_HEIGHT}
      backgroundScrollSpeed={10}
      {...renderConfig}
      >{content}</ParallaxScrollView>
  )
}

render() {
    const content = <View><Text>hello</Text></View>
    return this.renderView(content,{
      'name': 'React Native',
      'description':'React Native产出的并不是“网页应用”， 或者说“HTML5应用”，又或者“混合应用”。 最终产品是一个真正的移动应用',
      'avatar': 'https://bbs.reactnative.cn/uploads/profile/1-profileimg.png',
      'backgroundImg':'https://img0.sc115.com/uploads3/sc/jpgs/1904/zzpic17483_sc115.com.jpg'
    })
  }
```

#### 热更新 - react-native-pushy

```
https://github.com/reactnativecn/react-native-pushy/blob/master/docs/guide.md

1. 注册账号https://update.reactnative.cn

2.pushy login

3.创建应用
pushy createApp --platform ios
pushy createApp --platform android

3.发布应用
pushy uploadIpa <your-package.ipa>
pushy uploadApk android/app/build/outputs/apk/app-release.apk

4.发布新的热更新版本
pushy bundle --platform <ios|android>

```

#### 极光分享 - jshare-react-native 

#### 支付宝支付 - react-native-yunpeng-alipay

#### 微信支付 - react-native-wechat 

#### 配置相对路径
```
app文件下,新建一个package.json文件,

{
  "name":"app"
}
```