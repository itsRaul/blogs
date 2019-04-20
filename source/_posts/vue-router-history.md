---
title: vue-router的history模式
date: 2018-10-16
tags: ["vue-router"]
categories: vue
---

### History模式
<!-- more -->

### 简述
1.vue-router 默认 hash 模式 ,后面带有#号，如果不要hash，可以用路由的 history 模式，后面不带#号。

2.前端处理
```
const router = new VueRouter({
  mode: 'history',
  base:"/app"
  routes: [...]
})
```
base：应用的基路径。例如，如果整个单页应用服务在 /app/ 下，然后 base 就应该设为 "/app/"。如果放在服务器app文件下面没有base设置，页面会出现空白，无法显示页面。

3.history 模式需要后端的配置支持
[后端配置history模式详情](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90)

