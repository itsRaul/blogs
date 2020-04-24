---
title: JavaScript数组扁平化
tags: ['JavaScript']
categories: JavaScript
---

### JavaScript数组扁平化
<!-- more -->

> 数组的扁平化，就是将一个嵌套多层的数组 array (嵌套可以是任何层数)转换为只有一层的数组

#### 递归
> 循坏各个数组元素，如果还是一个数组，进行递归调用该方法

```
var arr = [1,[2,3],[4,[5,6]]]

function flatten(arr) {
    var result = []
    for (var i = 0, len = arr.length; i < len; i++) {
        //是否为数组，是则进行递归,数组合并
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]))
        }else {
            result.push(arr[i])
        }
    }
    return result
}

console.log(flatten(arr))
```

### reduce
> 对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，将其结果汇总为单个返回值

```
var arr = [1,[2,3],[4,[5,6]]]

function flatten(arr) {
    return arr.reduce(function(prev,next) {
        return prev.concat(Array.isArray(next) ? flatten(next) : next)
    },[])
}

console.log(flatten(arr))
```

### ES6 增加了扩展运算符
> ES6的扩展运算符只可以扁平一层，结合while和Array.some()

1. while
while 语句可以在某个条件表达式为真的前提下，循环执行指定的一段代码，直到那个表达式不为真时结束循环

2. Array.some()
测试数组中是不是至少有1个元素通过了被提供的函数测试。它返回的是一个Boolean类型的值(数组有1个满足条件，返回true。数组的元素全部不满足条件才返回false)

```
var arr = [1,[2,3],[4,[5,6]]]

function flatten(arr) {
    while(arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr)
    }
    return arr
}
console.log(flatten(arr))
```
