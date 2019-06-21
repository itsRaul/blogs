---
title: FlatList
date: 2019-06-21
tags: ['React Native']
categories: React Native
---

### react-native的FlatList
<!-- more -->

```
import React, {Component} from 'react';
import {StyleSheet, Text, View,FlatList,RefreshControl,ActivityIndicator} from 'react-native';

const CITY_NAMES = ['北京','上海','广州','深圳','佛山','清远','湛江']
type Props = {};
export default class FlatListDemo extends Component<Props> {
  constructor(props) {
    super(props)
    this.state = {
      isLoading: false,
      dataArray: CITY_NAMES,
    }
  }

  loadData(refreshing) {
    //如果是下拉刷新顶部loading转
    if (refreshing) {
      this.setState({
        isLoading: true,
      })
    }
  
    setTimeout( () => {
      let dataArray = []
      //判断是下拉刷新还是上拉下载
      if (refreshing) {
        for (let i = this.state.dataArray.length-1;i>=0;i--) {
          dataArray.push(this.state.dataArray[i])
        }
      }else {
        dataArray = this.state.dataArray.concat(CITY_NAMES)
      }
      
      this.setState({
        dataArray:dataArray,
        isLoading: false,
      })
    },1000)
  }

  _renderItem(data) {
    return (
      <View style={styles.item}>
        <Text style={styles.text}>{data.item}</Text>
      </View>
    )
  }

  genIndicator() {
    return (
      <View style={styles.indicatorContainer}>
        <ActivityIndicator
          style={styles.indicator}
          color={'red'}
          size={'large'}
          animating={true}
        />
        <Text>正在加载更多</Text>
      </View>
    )
  }


  //onRefresh：下拉刷新
  //refreshing：定义Loading状态
  //RefreshControl：这一组件可以用在ScrollView或FlatList内部，为其添加下拉刷新的功能
  //ListFooterComponent：尾部组件
  //onEndReached：当列表被滚动到距离内容最底部不足onEndReachedThreshold的距离时调用
  render() {
    return (
      <View style={styles.container}>
        <FlatList
          data={this.state.dataArray}
          renderItem={(data)=>this._renderItem(data)}
          // refreshing={this.state.isLoading}
          // onRefresh={ () => {
          //   this.loadData();
          // }}
          refreshControl={
            <RefreshControl
              title={'Loading'} //loading文字
              colors={'red'} //android的loading颜色
              tintColor={'red'} //ios的loading颜色
              titleColor={'red'} //loading文字颜色
              refreshing={this.state.isLoading}
              onRefresh={ () => {
                this.loadData(true)
              }}
            />
          }
          ListFooterComponent={ () => this.genIndicator()}
          onEndReached={ () => {
            this.loadData()
          }}
        />
      </View>
    );
  }
}
```
