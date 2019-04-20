---
title: 任意值Any
date: 2019-03-09 09:14:46
tags: ['TypeScript']
categories: TypeScript
---
### 任意值Any
<!-- more -->

### 任意值Any
任意值（Any）用来表示允许赋值为任意类型。
1.如果是一个普通类型，在赋值过程中改变类型是不被允许的。
2.如果是一个any类型,则允许被赋值为任意类型。
```
let myFavoriteNumber:any = 'seven';
myFavoriteNumber = 7;
```

### 任意值的属性和方法
1. 在任意值上访问任何属性都是允许的：
2. 也允许调用任何方法：
```
let anyThing: any = 'hello';
console.log(anyThing.myName);

let anyThing: any = 'Tom';
anyThing.setName('Jerry');
```

### 未声明类型的变量
变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型：
```
let something;
something = 'seven';
```

