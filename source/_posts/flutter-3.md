---
title: Flutter学习之路 - Flutter 常用的组件
date: 2020-06-02 15:09:46
tags: ['Flutter']
categories: Flutter
---

### Flutter 常用的组件

<!-- more -->

### Flutter实例项目

1.https://github.com/flutter/flutter/tree/master/examples

2.https://github.com/flutter/samples

3.https://github.com/nisrulz/flutter-examples

4.https://github.com/iampawan/FlutterExampleApps

### BottomNavigationBar 实践

配置BottomNavigationBar，根据更新索引值，PageView切换不同页面

```
import 'package:flutter/material.dart';
import 'package:flutter_trip/pages/home_page.dart';
import 'package:flutter_trip/pages/my_page.dart';
import 'package:flutter_trip/pages/search_page.dart';
import 'package:flutter_trip/pages/travel_page.dart';

class TabNavigator extends StatefulWidget {
  @override
  _TabNavigatorState createState() => _TabNavigatorState();
}

class _TabNavigatorState extends State<TabNavigator> {
  final _defaultColor = Colors.grey;
  final _activeColor = Colors.blue;
  int _currentIndex = 0;
  final PageController _controller = PageController();
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView(
        controller: _controller,
        children: <Widget>[
          HomePage(),
          SearchPage(),
          TravelPage(),
          MyPage(),
        ],
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex, //当前索引
        onTap: (index) {
          _controller.jumpToPage(index); //不同的index对应不同页面
          setState(() {
            _currentIndex = index;
          });
        }, 
        type: BottomNavigationBarType.fixed,
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.home, color: _defaultColor),
            activeIcon: Icon(Icons.home, color: _activeColor),
            title: Text(
              '首页',
              style: TextStyle(
                color: _currentIndex != 0 ? _defaultColor : _activeColor
              )
            )
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.search, color: _defaultColor),
            activeIcon: Icon(Icons.search, color: _activeColor),
            title: Text(
              '搜索',
              style: TextStyle(
                color: _currentIndex != 1 ? _defaultColor : _activeColor
              )
            )
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.camera_alt, color: _defaultColor),
            activeIcon: Icon(Icons.camera_alt, color: _activeColor),
            title: Text(
              '旅拍',
              style: TextStyle(
                color: _currentIndex != 2 ? _defaultColor : _activeColor
              )
            )
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.account_circle, color: _defaultColor),
            activeIcon: Icon(Icons.account_circle, color: _activeColor),
            title: Text(
              '我的',
              style: TextStyle(
                color: _currentIndex != 3 ? _defaultColor : _activeColor
              )
            )
          ),
        ] 
      ),
    );
  }
}

```

### 轮播图

https://github.com/best-flutter/flutter_swiper/blob/master/README-ZH.md

```
flutter_swiper: ^1.1.4

import 'package:flutter/material.dart';
import 'package:flutter_swiper/flutter_swiper.dart';

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  List _imageUrls = [
    'http://via.placeholder.com/350x150',
    'http://via.placeholder.com/350x150',
    'http://via.placeholder.com/350x150'
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          children:<Widget>[
            Container(
              height: 160,
              child: Swiper(
                itemCount: _imageUrls.length,
                autoplay: true,
                itemBuilder: (BuildContext context, int index) {
                  return Image.network(
                    _imageUrls[index],
                    fit: BoxFit.fill
                  );
                },
                pagination:SwiperPagination(),
              )
            )
          ]
        ),
      ),
    );
  }
}

```

### 滚动渐变

使用ListView组件进行滚动，NotificationListener()监听滚动的距离, MediaQuery.removePadding去除顶部的距离。Stack进行层叠布局，Widget越后创建，层级越靠上。

```
import 'package:flutter/material.dart';
import 'package:flutter_swiper/flutter_swiper.dart';

const APPBAR_SCROLL_OFFSET = 100;

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  List _imageUrls = [
    'http://via.placeholder.com/350x150',
    'http://via.placeholder.com/350x150',
    'http://via.placeholder.com/350x150'
  ];

  double appBarAlpha = 0;

  //滚动函数
  _onScroll(offset) {
    double alpha = offset / APPBAR_SCROLL_OFFSET;
    print(alpha);
    if (alpha < 0) {
      alpha = 0;
    }else if (alpha > 1) {
      alpha = 1;
    }
     
    setState(() {
      appBarAlpha = alpha;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(
        children: <Widget> [
          MediaQuery.removePadding(
            removeTop: true,
            context: context, 
            child: NotificationListener(
              onNotification: (scrollNotification) {
                if (scrollNotification is ScrollNotification && scrollNotification.depth == 0) {
                  _onScroll(scrollNotification.metrics.pixels);
                 
                }    
              },
              child: ListView(
                children: <Widget>[
                  Container(
                    height: 160,
                    child: Swiper(
                      itemCount: _imageUrls.length,
                      autoplay: true,
                      itemBuilder: (BuildContext context, int index) {
                        return Image.network(
                          _imageUrls[index],
                          fit: BoxFit.fill
                        );
                      },
                      pagination:SwiperPagination(),
                    )
                  )
                ]
              )
            )
          ),
          Opacity(
            opacity: appBarAlpha,
            child: Container(
              height: 60,
              decoration: BoxDecoration(color: Colors.white),
              child: Center(
                child: Padding(
                  padding: EdgeInsets.only(top: 20),
                  child: Text('首页')
                )
              ),
            ),
          ),
        ]
      )
    );
  }
}

```
