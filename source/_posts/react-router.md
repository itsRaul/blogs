---
title: React RouterV4
date: 2019-02-24 20:27:40
tags: ["React"]
categories: React
---
### React Router
<!-- more -->

### react-router和react-router-dom理解
1. react-router
提供了一些router的核心api,包括Router,Route,Switch等

2.react-router-dom
提供了BrowserRouter,HashRouter,Route,Link,NavLink

### react-router-dom核心用法
1.HashRouter和BrowserRouter

2.Route:path,exact,component,render
Route:路由的根基
path:路由的匹配
exact:精准匹配
component:渲染的 React 组件
render:方便的内联渲染和包裹

3.NavLink,Link
Link:导航，路由跳转

4.Switch
渲染与该地址匹配的第一个子节点 <Route>,匹配到不会往下继续匹配。

5.Redirect
路由重定向

6.HashRouter和BrowserRouter
HashRouter: http://localhost:3000/#/admin，带有#号
BrowserRouter:http://localhost:3000/admin,基于浏览的Router


