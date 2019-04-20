---
title: this
date: 2019-04-02
tags: ['你不知道的JavaScript']
categories: 你不知道的JavaScript
---
### 关于this
<!-- more -->

> this 既不指向函数自身也不指向函数的词法作用域。this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

### this是什么
当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包 
含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this 就是记录的 
其中一个属性，会在函数执行的过程中用到。

### this绑定
1.默认绑定
```
function foo() {
    console.log(this.a)
}
var a = 2;
foo() //2
```
调用foo()，this.a被解析成全局变量a。函数调用时应用了this的默认绑定,this指向全局对象。
如何判断默认绑定？在代码中，foo() 是直接使用不带任何修饰的函数引用进行调用的，因此只能使用 
默认绑定，无法应用其他规则。
严格模式下this是undefined。

2.隐式绑定
```
function foo() {
    console.log(this)
}
var obj = {
    a: 2,
    foo:foo
}
var a  = 3;
obj.foo() //2
```
foo()是被当作引用属性添加到 obj 中的。
调用位置会使用obj上下文引用函数，foo()调用时指向obj对象，隐式规则会把函数调用中的this绑定到这个上下文对象

3.显式绑定
```
function foo() { 
    console.log( this.a ); 
} 
var obj = { 
    a:2 
}; 
var a = 3;
foo.call( obj ); // 2
```
通过  foo.call(..)，我们可以在调用  foo 时强制把它的  this 绑定到  obj 上。
从  this 绑定的角度来说，call(..) 和  apply(..) 是一样的，它们的区别体现 
在其他的参数上。

4.new绑定
使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。
1.创建（或者说构造）一个全新的对象。 
2.这个新对象会被执行  [[ 原型  ]] 连接。 
3.这个新对象会绑定到函数调用的 this。 
4.如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

---
```
function foo(a) { 
    this.a = a; 
} 
var bar = new foo(2); 
console.log( bar.a ); // 2
```
使用  new 来调用  foo(..) 时，我们会构造一个新对象并把它绑定到  foo(..) 调用中的  this 
上。new 是最后一种可以影响函数调用时  this 绑定行为的方法，我们称之为  new 绑定。

### 箭头函数
> 箭头函数根据外层（函数或者全局）作用域来决定 this。 

```
function foo() { 
    // 返回一个箭头函数 
    return (a) => { 
        //this 继承自foo() 
        console.log( this.a ); 
    }; 
} 
var obj1 = { 
    a:2 
};
var obj2 = { 
    a:3
};
var bar = foo.call(obj1)
bar.call(obj2) //2 不是3
```
箭头函数会捕获调用时foo()到this,foo()的this绑定obj1，this也绑定到obj1。
箭头函数的绑定无法被修改

### 判断this
1.  函数是否在 new 中调用（new 绑定）？如果是的话  this 绑定的是新创建的对象。 
    var bar = new foo() 
2.  函数是否通过  call、apply（显式绑定）或者硬绑定调用？如果是的话，this 绑定的是指定的对象。
    var bar = foo.call(obj2) 
3.  函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上下文对象。  
    var bar = obj1.foo() 
4.  如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到全局对象。 
    var bar = foo()

