---
title: TypeScript
date: 2019-03-09 08:47:56
tags: ['TypeScript']
categories: TypeScript
---
### TypeScript
<!-- more -->

### TypeScipt介绍
> TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript。编译出来的 JavaScript 可以运行在任何浏览器上。TypeScript 编译工具可以运行在任何服务器和任何系统上。TypeScript 是开源的。

TypeScript 是 JavaScript 的一个超集，主要提供了类型系统和对 ES6 的支持，它由 微软 开发。

### TS 好处
1.代码的可维护性。类型系统实际上是最好的文档。
2.非常包容，TS是JS的超集。
3.活跃的社区。

### Hello TypeScript
```
function sayHello(person:string) {
    return 'Hello,' + person;
}
let user = 'Tom';
```
编译成.js文件
```
function sayHello(person) {
    return 'Hello, ' + person;
}
var user = 'Tom';
console.log(sayHello(user));
```
使用 : 指定变量的类型，: 的前后有没有空格都可以。
