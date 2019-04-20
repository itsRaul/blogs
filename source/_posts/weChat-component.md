---
title: 小程序组件
date: 2018-11-04
tags: ['小程序']
categories: 小程序
---

### 小程序组件、事件与属性
<!-- more -->

### 组件引用
```
Component({
    /**
    * 组件的属性列表，外部暴露的属性
    */
    properties: {
        like:{
            type:Number、String、Boolean, //属性的类型，必填
            value:false, //初始值，选填
            observer: function (newVal, oldVal) { 
                this.setData({
                    val:newVal
                })
            } 
            // observer属性被改变时执行的函数（可选)，不要直接去改变properties的值(发生内存泄漏)，通过data定义的数据，通过setData把properties的值赋值data定义的值
        }
    },

    /**
    * 组件的初始数据
    */
    data: {
        isLike:false,
        val:null
    },

    /**
    * 组件的方法列表
    */
    methods: {
        this.properties.like//访问properties的属性
    }
})
```
1.微信开发者工具小程序新建Component,新建组件目录
2.properties：对外属性，即如果外部的wxml文件传入数据时，会把数据设置成properties的属性。(类似vue父子组件的传值类型限定)
3.data数据的状态绑定
4.methods组件的方法和生命周期的使用
5.只有setData才可以更新data的数据