---
title: react-native的全面屏处理
date: 2019-06-25
tags: ['React Native']
categories: React Native
---

### react-native的全面屏处理
<!-- more -->

#### iOS
以iPhoneX为例，顶部导航栏和底部导航栏有一个“安全”区域，怎么去处理？RN官方提供SafeAreaView的组件目的是在一个“安全”的可视区域内渲染内容。对SafeAreaView进行封装处理

```
/** 
 * topColor 顶部颜色
 */
import React, {Component,} from 'react';
import {DeviceInfo, SafeAreaView, StyleSheet, View, ViewPropTypes} from 'react-native';
import {PropTypes} from 'prop-types';

export default class SafeAreaViewPlus extends Component {
  static propTypes = {
    ...ViewPropTypes,
    topColor: PropTypes.string,
    bottomColor: PropTypes.string,
    enablePlus: PropTypes.bool,
    topInset: PropTypes.bool,
    bottomInset: PropTypes.bool,

  };
  static defaultProps = {
    topColor: 'transparent',
    bottomColor: '#f8f8f8',
    enablePlus: true,
    topInset: true,
    bottomInset: false,
  };

  genSafeAreaViewPlus() {
    const {children, topColor, bottomColor, topInset, bottomInset} = this.props;
    return <View style={[styles.container, this.props.style]}>
      {this.getTopArea(topColor, topInset)}
      {children}
      {this.getBottomArea(bottomColor, bottomInset)}
    </View>;
  }

  genSafeAreaView() {
    return <SafeAreaView style={[styles.container, this.props.style]} {...this.props}>
        {this.props.children}
    </SafeAreaView>
  }

  getTopArea(topColor, topInset) {
    return !DeviceInfo.isIPhoneX_deprecated || !topInset ? null
        : <View style={[styles.topArea, {backgroundColor: topColor}]}/>;
  }

  getBottomArea(bottomColor, bottomInset) {
    return !DeviceInfo.isIPhoneX_deprecated || !bottomInset ? null
        : <View style={[styles.bottomArea, {backgroundColor: bottomColor}]}/>;
  }

  render() {
    const {enablePlus} = this.props;
    return enablePlus ? this.genSafeAreaViewPlus() : this.genSafeAreaView();
  }
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  topArea: {
    height: 44,
  },
  bottomArea: {
    height: 34,
  }
});
```

#### Android
改变屏幕最大适应比例

```
AndroidManifest.xml

<!--适配全面屏-->
android:resizeableActivity="true"
<meta-data
    android:name="android.max_aspect"
    android:value="2.2" />
```