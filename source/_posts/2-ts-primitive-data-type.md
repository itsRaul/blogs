---
title: 原始数据类型
date: 2019-03-09 09:01:32
tags: ['TypeScript']
categories: TypeScript
---
### 原始数据类型
<!-- more -->

### 原始数据类型
1.JS的类型分为两种，原始数据类型和对象类型。
2.原始数据类型分为五种，布尔值、数值、字符串、null、undefind、以及ES6中的新类型Symbol。

### TypeScript使用原始类型
1.布尔值
```
let isDone: boolean = false;

// 编译通过
// 后面约定，未强调编译错误的代码片段，默认为编译通过
```
2.数值
```
let decLiteral: number = 6;
```

3.字符串
```
let myName:string = 'Tom';
```
4.空值
JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 void 表示没有任何返回值的函数：
```
function alertName(): void {
    alert('My name is Tom')
}
```
声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null：
```
let unusable: void = undefined;
```

5.Null 和 Undefined
在 TypeScript 中，可以使用 null 和 undefined 来定义这两个原始数据类型：
```
let u: undefined = undefined;
let n: null = null;
```
undefined 类型的变量只能被赋值为 undefined，null 类型的变量只能被赋值为 null。
与 void 的区别是，undefined 和 null 是所有类型的子类型。也就是说 undefined 类型的变量，可以赋值给 number 类型的变量：
```
// 这样不会报错
let num: number = undefined; 
let u: undefined;
let num: number = u;
```
而 void 类型的变量不能赋值给 number 类型的变量：
```
let u: void;
let num: number = u;

// index.ts(2,5): error TS2322: Type 'void' is not assignable to type 'number'.
```


