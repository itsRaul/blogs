---
title: 对象的类型——接口
date: 2019-03-09 09:27:54
tags: ['TypeScript']
categories: TypeScript
---
### 对象的类型(interfacer)
<!-- more -->

### 什么是接口
对行为的抽象，而具体如何行动需要由类去实现。TypeScript 中的接口是一个非常灵活的概念，除了可用于对类的一部分行为进行抽象以外，也常用于对「对象的形状（Shape）」进行描述。
```
//定义接口
interface Person {
    name:string;
    age: number;
}

//定义一个变量，类型是Person。约束了 tom 的形状必须和接口 Person 一致。接口少了一些属性是不允许的，多一些属性也是不允许的。
let tom:Person = {
    name: 'Tom',
    age: 25
}
```

### 可选属性
有时不需要匹配所有的属性，可以用可选属性。定义属性key后面加? 如key?
```
//定义接口
interface Person {
    name:string;
    age？: number;
}

//这时仍然不允许添加未定义的属性：
let tom: Person = {
    name: 'Tom'
};
```

### 任意属性
1.使用 [propName: string] 定义了任意属性取 string 类型的值。
2.一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集，负责报错。

```
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```
```
//报错
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
```
因为可选属性age的值是number,number 不是 string 的子属性，所以报错了。

### 可读属性
1.readonly 定义只读属性：
2.注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候：
```
//报错
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527;
```
使用 readonly 定义的属性 id 初始化后，又被赋值了，所以报错了。

```
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};

tom.id = 89757;
```
报错如下：
1. tom 进行赋值的时候，没有给 id 赋值。
2.  tom.id 赋值的时候，由于它是只读属性