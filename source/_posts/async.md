---
title: JavaScript 的 async/await
date: 2018-12-28 
tags: ['JavaScript']
categories: JavaScript
---



### 什么是async、await
<!-- more -->

async 是“异步”的简写，而 await 可以认为是 async wait 的简写。所以应该很好理解 async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。

### async
1.async用法，它作为一个关键字放到函数前面，用于表示函数是一个异步函数，因为async就是异步的意思， 异步函数也就意味着该函数的执行不会阻塞后面代码的执行。 写一个async 函数
```
async function test() {
    return "hello world"
}
```
2.语法就是在函数前面加上async关键字，用来表示它是异步的，怎么调用呢？async 函数也是函数，平时我们怎么使用函数就怎么使用它，直接加括号调用就可以了，为了表示它没有阻塞它后面代码的执行。
```
console.log(test())
//输出的是一个Promisea对象

Promise
__proto__: Promise
[[PromiseStatus]]: "resolved"
[[PromiseValue]]: "hell"
```

3.async 函数返回的是一个 Promise 对象。从文档中也可以得到这个信息。async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，如果在函数中 return 一个直接量，async 会把这个直接量通过 Promise.resolve() 封装成 Promise 对象。

### await
1.一般来说，都认为 await 是在等待一个 async 函数完成。不过按语法说明，await 等待的是一个表达式，这个表达式的计算结果是 Promise 对象或者其它值（换句话说，就是没有特殊限定）。

2.async函数返回的是一个Promise对象，所以await可以用于等待一个async函数的返回值，await就是等待async的返回值。

```
function test1() {
    reuturn "test1"
}
function test2() {
    reuturn "test2"
}
async function test() {
    const v1 = await test1();
    const v2 = await test2();
}
test()
```

3.async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。

### async/await 干了啥
1.async会将其后的函数的返回值封装成一个Promise对象，而await会等待这个Promise完成，并将其resolve的结果返回出来。

2.async/await 的优势在于处理 then 链，单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了
```
/**
 * 传入参数 n，表示这个函数执行的时间（毫秒）
 * 执行的结果是 n + 200，这个值将用于下一步骤
 */
function takeLongTime(n) {
    return new Promise(resolve => {
        setTimeout(() => resolve(n + 200), n);
    });
}

function step1(n) {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}

function step2(n) {
    console.log(`step2 with ${n}`);
    return takeLongTime(n);
}

function step3(n) {
    console.log(`step3 with ${n}`);
    return takeLongTime(n);
}
```

现在用 Promise 方式来实现这三个步骤的处理
```
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}

doIt();
```

用 async/await 来实现
```
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const result = await step3(time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();
```

### 捕捉错误
既然.then(..)不用写了，那么.catch(..)也不用写，可以直接用标准的try catch语法捕捉错误。
```
const start = async function () {
    try {
        console.log('start');
        await sleep(3000); // 这里得到了一个返回错误
        
        // 所以以下代码不会被执行了
        console.log('end');
    } catch (err) {
        console.log(err); // 这里捕捉到错误 `error`
    }
};
```


