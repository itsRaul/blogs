---
title: 提升和作用域闭包
date: 2019-04-1 
tags: ['你不知道的JavaScript']
categories: 你不知道的JavaScript
---
### 提升和作用域闭包
<!-- more -->

### 提升
> 这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。
可以将这个过程形象地想象成所有的声明（变量和函数）都会被“移动”到各自作用域的
最顶端，这个过程被称为提升。
> 声明本身会被提升，而包括函数表达式的赋值在内的赋值操作并不会提升。 
要注意避免重复声明，特别是当普通的  var 声明和函数声明混合在一起的时候，否则会引 
起很多危险的问题！

```
a = 2;
var a;
console.log(a) //2
------------------
进行如下处理
var a;
a = 2;
变量出现的位置被“移动”到了最上面，这个过程就叫做提升
```
变量和函数在内的所有声明都会在任何代码被执行前首先被处理。JavaScript实际会将其看两个声明：var a;和a = 2;第一个定义声明是在编译阶段进行，第二个赋值声明会被留在原地等待执行阶段。

```
foo()
function foo() {
    console.log(a) //undefined
    var a = 2;
}
--------------
进行如下处理
function foo() {
    var a;
    console.log(a) //undefined
    a = 2;
}
foo()
```
foo函数声明被提升了，但函数表达式却不会被提升。
```
foo() //TypeError
var foo = function bar() {
    //...
}
```
即使是具名函数表达式，名称标识符在赋值之前也无法在所在作用域中使用。

```
foo() //1
var foo;
function foo() {
    console.log(1)
}
foo = function() {
    console.log(2)
}
-----------
进行如下处理
function foo() {
    console.log(1)
}
foo() //1
foo = function() {
    console.log(2)
}
```
函数声明和变量都会被提升，函数首先被提升，然后才是变量。后面的函数声明还可以覆盖前面的函数声明

```
foo() //3

function foo() {
    console.log(1)
}

var foo = function() {
    console.log(2)
}

function foo() {
    console.log(3)
}
```

### 作用域闭包
> 闭包就是能够读取其他函数内部变量的函数。由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

```
function foo() {
    var a = 2;
    function bar() {
        console.log(a)
    }
    return bar
}
var baz = bar();
baz(); //2
```
函数baz()能够访问foo()的内部作用域。将bar()函数本身当作一个值类型进行传递。而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此 
没有被回收。谁在使用这个内部作用域？原来是  bar() 本身在使用。bar()拥有foo()内部作用域的闭包，该作用域一直存活,bar()本身在使用。bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

