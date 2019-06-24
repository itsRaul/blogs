---
title: react-native在Android物理回退键的问题
date: 2019-06-24
tags: ['React Native']
categories: React Native
---

### BackHandler
<!-- more -->

BackHandler：监听设备上的后退按钮事件
Android：监听后退按钮事件。如果没有添加任何监听函数，或者所有的监听函数都返回 false，则会执行默认行为，退出应用。

```
import React, {PropTypes} from "react";
import {BackHandler} from "react-native";

/**
 * Android物理回退键处理
 */
export default class BackPressUtil {
  constructor(props) {
    this._hardwareBackPress = this.onHardwareBackPress.bind(this);
    this.props = props;
  }

  componentDidMount() {
    if (this.props.backPress) BackHandler.addEventListener('hardwareBackPress', this._hardwareBackPress);
  }

  componentWillUnmount() {
    if (this.props.backPress) BackHandler.removeEventListener('hardwareBackPress', this._hardwareBackPress);
  }

  onHardwareBackPress(e) {
    return this.props.backPress(e);
  }
}

page.js
在返回页面调用navigation的goBack方法返回页面
constructor(props, updateState) {
  this.props = props;
  this.backPress = new BackPress({backPress: () => this.onBackPress()});
}

onBackPress() {
  NavigationUtil.goBack(this.props.navigation);
}  
```