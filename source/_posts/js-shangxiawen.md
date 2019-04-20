---
title: JavaScript的作用域和变量提升、函数声明
date: 2019-01-03 23:03:37
tags: ['JavaScript']
categories: JavaScript
---

### 什么是作用域
<!-- more -->
1. 作用域就是指程序源代码定义变量的区域。
2. 作用域规定如何查找变量，确定对当前执行代码对变量对访问权限。
```
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();
```
首先是bar()函数执行，在bar()函数内部foo()函数执行，先在foo函数对当前作用域进行查找，如果没有，则查找上面一层的代码，也就是 value 等于 1，所以结果会打印 1。

### 顺序执行
```
var foo = function () {

    console.log('foo1');

}

foo();  // foo1

var foo = function () {

    console.log('foo2');

}

foo(); // foo2
```
```
function foo() {

    console.log('foo1');

}

foo();  // foo2

function foo() {

    console.log('foo2');

}

foo(); // foo2
```
 JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一段代码的时候，会进行一个“准备工作”，比如第一个例子中的变量提升，和第二个例子中的函数提升。

 ### 变量提升
 1. 通常JS引擎会在正式执行之前先进行一次预编译，在这个过程中，首先将变量声明及函数声明提升至当前作用域的顶端，然后进行接下来的处理。
 2. 如果在当前作用域存在此声明对变量，无论它在什么地方声明，引用此变量时就会在当前作用域中查找，不会去外层作用域了。
 ```
var foo = 3;

function hoistVariable() {

    var foo = foo || 5;

    console.log(foo); // 5
}

hoistVariable();
 ```
 虽然外层作用域有个foo变量，但函数内是不会去引用的。先在当前作用域寻找foo变量，如果当前作用域foo不存在，则通过作用域链往上继续找foo变量，如果继续不存在而又未定义则报错。

 ### 函数提升
 1. 为什么函数可以在声明之前就可以调用，并且跟变量声明不同的是，它还能得到正确的结果，其实引擎是把函数声明整个地提升到了当前作用域的顶部
 ```
 function hoistFunction() {
    foo(); // output: I am hoisted

    function foo() {
        console.log('I am hoisted');
    }
}

hoistFunction();
 ```
 2. 如果在同一个作用域中存在多个同名函数声明，后面出现的将会覆盖前面的函数声明：
 ```
 function hoistFunction() {
    function foo() {
        console.log(1);
    }

    foo(); // output: 2

    function foo() {
        console.log(2);
    }
}

hoistFunction();
 ```
 3. 函数声明和函数表达式
 ```
// 函数声明
function foo() {
    console.log('function declaration');
}

// 匿名函数表达式
var foo = function() {
    console.log('anonymous function expression');
};

// 具名函数表达式
var foo = function bar() {
    console.log('named function expression');
};
 ```
1. 匿名函数表达式，其实是将一个不带名字的函数声明赋值给了一个变量，
2. 具名函数表达式，则是带名字的函数赋值给一个变量，需要注意到是，这个函数名只能在此函数内部使用。
3. 函数表达式可以通过变量访问，所以也存在变量提升同样的效果。
4. 函数的优先权是最高的，它永远被提升至作用域最顶部，然后才是函数表达式和变量按顺序执行
