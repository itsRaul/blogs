---
title: React
date: 2019-02-21 22:06:54
tags: ["React"]
categories: React
---
### React介绍
<!-- more -->

### 概述
1. Fackbook开源的一个JavaScript库
2. React结合生态库构成一个MVC框架
3. React特点
· Declarative(声明式编码)
· Component-Based(组件化编码)
    组件化、复用
· 高效-高效的DOM Diff算法，最小化页面重绘
    通过创建虚拟DOM的方式，不是直接操作DOM，而是通过构建JavaScript的语法，拼接DOM对象，一次性插入DOM
· 单向数据流
    Vue在输入框表单上是双向数据流。而React都是单向数据流，自上而下的数据传递，“从一棵树的根流向它的叶子”

### 生态介绍
· React + React-Router + Redux + Axios + Babel + Webpack

### 编程式和声明式
1. 编程式实现
· 需要以具体代码表达在哪里(where)做什么(what),如何实现(how)
2. 声明式实现
· 只需要声明在哪里(where)做什么(what)，而无需关心如何实现(how)

### 生命周期
![image](/images/react.png)
1. getDefaultProps
· 初始化一个props属性，props来自父组件或其它组件传递过来的
2. getInitalState
· 组件的初始化状态
3. componentWillMount()
· 组件初始化之前调用这个方法，此时可以进行开启定时器、向服务器发送请求等操作
4. render()
· 组件渲染
5. componentDidMount
· 组件DOM插入完调用的方法，此时页面中有了真正的DOM的元素，可以进行DOM相关的操作
6. componentWillReceiveProps()
· 来自父组件传递属性调用的方法。组件接收到属性时触发
7. shouldComonentUpdate()
· 组件的更新，调用setState方法就会这个生命周期。组件的状态发生改变时触发
8. componentWillUpdate()
· 组件即将被更新时触发
9. componentDidUpdate()
· 组件被更新完成后触发。页面中产生了新的DOM的元素，可以进行DOM操作
10. componentWillUnmount
· 组件被销毁时触发。这里我们可以进行一些清理操作，例如清理定时器，取消Redux的订阅事件等等。





