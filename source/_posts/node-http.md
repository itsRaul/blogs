---
title: node-http
date: 2019-05-29 23:40:30
tags: ['Node.js']
categories: Node.js
---

### Node.js处理http请求
<!-- more -->

### 输入url，发生什么
1. DNS解析，建立TCP连接，发送http请求
DNS解析：通过域名解析到一个ip地址，Remote Address（ip地址）

2. server接收到http请求，处理，并返回

3. 客户端收到返回数据，处理数据（如渲染页面，执行js）

### 处理get请求
#### 示例
```
const http = require('http')

const server = http.createServer( (req,res) => {
    res.end('hello world')
})

server.listen(8000)
```

#### get请求
1. get请求，即客户端要向server端获取数据，如查询列表
2. 通过querystring来传递数据，如a.html?a=100&b=200
3. 浏览器直接访问，就发送get请求 
```
const http = require('http')
const querystring = require('querystring')

const server = http.createServer( (req,res) => {
    // req.method 请求方法
    const url = req.url //获取请求端完整url
    req.query = querystring.parse(url.split(?)[1]) //解析querystring
    res.end(JSON.stringify(req.query)) 将querystring返回
})

server.listen(8000)
```

### post请求
1. post请求，即客户端要像服务端传递数据，如新建

2. 通过post data传递数据，后面会演示

3. 浏览器无法直接模拟，需要手写js，活着使用postman
```
const http = require('http')

const server = http.createServer( (req.res) => {
    if (req.methods === "POST") {
        //req数据格式
        console.log('req content-type',req.headers['content-type'])

        let postData = ''
        req.on('data', chunk => {
            postData += chunk.toString()
        })

         req.on('on', chunk => {
            res.end('hello word')
        })

    }
})
```

### node处理http请求
```
const http = require('http')

const querystring = require('querystring')
/**
 * url : http://localhost:8000/api/bolg/list?author=zhangsan&key=A
 *
 */
server = http.createServer( (req,res) => {
    const method = req.method
    const url = req.url
    const path = url.split('?')[0]
    const query = querystring.parse(url.split('?')[1])

    //设置返回格式为JSON
    res.setHeader('Content-type','application/json')

    //返回的数据
    const resData = {
        method,
        url,
        path,
        query
    }

    //返回
    if (method === "GET") {
        res.end(JSON.stringify(resData))
    }

    if (req.methods === "POST") {

        let postData = ''
        req.on('data', chunk => {
            postData += chunk.toString()
        })

         req.on('end', () => {
            resData.postData = postData
            res.end(JSON.stringify(resData))
        })

    }
    
    //{"method":"GET","url":"/api/bolg/list?author=zhangsan&key=A","path":"/api/bolg/list","query":{"author":"zhangsan","key":"A"}}

})

server.listen(8000)

```
