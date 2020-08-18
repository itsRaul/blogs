---
title: Flutter学习之路 - 列表的使用
date: 2020-06-05 09:58:42
tags: ['Flutter']
categories: Flutter
---

### 列表的使用

<!-- more -->

### 基础列表的使用

1.scrollDirection: Axis.horizontal 列表的横行滚动

2.toList()转成List列表

```
const CITY_NAMES = ['北京','上海','广州','深圳','杭州','苏州','武汉','西安','西藏'];

class SearchPage extends StatefulWidget {
  @override
  _SearchPageState createState() => _SearchPageState();
}

class _SearchPageState extends State<SearchPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        height: 100,
        child:ListView(
          scrollDirection: Axis.horizontal,
          children:  _buildList()
        )
      )
    );
  }

  List<Widget>_buildList() {
    return CITY_NAMES.map((city) => _item(city)).toList();
  }
  
  Widget _item(city) {
    return Container(
      width: 160,
      margin: EdgeInsets.only(right:15),
      alignment:Alignment.center,
      decoration: BoxDecoration(color: Colors.teal),
      child: Text(
        city,
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    );
  }
}

```

### 列表的展开和收起

定义widgets的List，遍历keys，使用ExpansionTile的Widget来展开和收起

```
const CITY_NAMES = {
  '北京': ['东城区','西城区','朝阳区','丰台区','石景山区'],
  '上海': ['黄浦区', '徐汇区','长宁区','静安区'],
  '广州': ['越秀','海珠','荔湾'],
};

class SearchPage extends StatefulWidget {
  @override
  _SearchPageState createState() => _SearchPageState();
}

class _SearchPageState extends State<SearchPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        child:ListView(
          children:  _buildList()
        )
      )
    );
  }

  List<Widget>_buildList() {
    List<Widget> widgets = [];
    CITY_NAMES.keys.forEach((key) {
      widgets.add(_item(key,CITY_NAMES[key]));
    });
    return widgets;
  }
  
  Widget _item(String city, List<String> subCities) {
    return ExpansionTile(
      title: Text(
        city,
        style: TextStyle(color: Colors.black54,fontSize: 20),
      ),
      children: subCities.map((subCity) => _buildSub(subCity)).toList()
    );
  }

  Widget _buildSub(String subCity) {
    return FractionallySizedBox(
      widthFactor: 1,
      child: Container(
        height: 50,
        margin: EdgeInsets.only(bottom:5),
        decoration: BoxDecoration(color: Colors.black54),
        child: Text(subCity),
      ),
    );
  }
}
```

### 网格布局

使用GridView.count进行网格布局

```
const CITY_NAMES = ['北京','上海','广州','深圳','杭州','苏州','武汉','西安','西藏'];

class SearchPage extends StatefulWidget {
  @override
  _SearchPageState createState() => _SearchPageState();
}

class _SearchPageState extends State<SearchPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GridView.count(
          crossAxisCount: 4,
          children: _buildList(),
        )
    );
  }

  List<Widget>_buildList() {
    return CITY_NAMES.map((city) => _item(city)).toList();
  }
  
  Widget _item(city) {
    return Container(
      height: 80,
      margin: EdgeInsets.only(bottom: 5),
      alignment:Alignment.center,
      decoration: BoxDecoration(color: Colors.teal),
      child: Text(
        city,
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    );
  }
}

```

### 下拉刷新和上拉加载

1.下拉刷新使用RefreshIndicator里的onRefresh回调

2.上拉加载添加ScrollController(), 在生命周期开始initState判断页面当前滚动位置等于页面最大滚动位置，执行加载更多当函数。在生命周期结束dispose将之前当监听移除，减少消耗，提升性能。

```
const CITY_NAMES = ['北京','上海','广州','深圳','杭州','苏州','武汉','西安','西藏'];

class SearchPage extends StatefulWidget {
  @override
  _SearchPageState createState() => _SearchPageState();
}

class _SearchPageState extends State<SearchPage> {

  List<String> cityNames =  ['北京','上海','广州','深圳','杭州','苏州','武汉','西安','西藏'];

  ScrollController _scrollController = ScrollController();

  @override
  void initState() {
    _scrollController.addListener(() {
      if(_scrollController.position.pixels == _scrollController.position.maxScrollExtent) {
        _loadData();
      }
    });
    super.initState();
  }

  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }

  _loadData() async {
    await Future.delayed(Duration(milliseconds: 200));

    setState(() {
      List<String> list = List<String>.from(cityNames);
      list.addAll(cityNames);
      cityNames = list;
    });

    print('上拉加载');
  }

  Future<Null> _handleRefresh() async {
    await Future.delayed(Duration(seconds: 2));
    setState(() {
      cityNames = cityNames.reversed.toList();
    });
    print('下拉刷新');
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: RefreshIndicator(
        child: ListView(
          controller: _scrollController,
          children: _buildList()
        ), 
        onRefresh: _handleRefresh
      )
    );
  }
  
  List<Widget>_buildList() {
    return cityNames.map((city) => _item(city)).toList();
  }
  
  Widget _item(city) {
    return Container(
      height: 80,
      margin: EdgeInsets.only(bottom: 5),
      alignment:Alignment.center,
      decoration: BoxDecoration(color: Colors.teal),
      child: Text(
        city,
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    );
  }
}
```
