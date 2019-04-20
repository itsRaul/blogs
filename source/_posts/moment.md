---
title: moment在vue时间格式化
date: 2018-10-10
tags: ["vue"]
categories: vue
---
### Vue--moment时间格式插件安装和使用
<!-- more -->

### [moment文档](http://momentjs.cn/)

### 安装插件
```
yarn add moment -S
```

### 导入使用
```
import moment from 'moment'

filters: {
    dateyear(time){
        return moment(time*1000).format("YYYY年MM月DD日")
    },
    formatDate(time) {
        return moment(time*1000).format("YYYY-MM-DD HH:mm:ss")
    }
},
```
filters过滤器，time时间戳的形参，time为什么要乘以1000？因为我公司的后端是PHP，PHP返回的时间戳需要乘以1000,格式化的时间才准确显示。
```
<template>
    <div>{{item.add_time | formatDate}}</div>
</template>
```
在vue模版时间格式化的显示