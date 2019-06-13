---
title: Node.js的数据存储
date: 2019-06-12
tags: ['Node.js']
categories: Node.js
---

### Node.js的数据存储
<!-- more -->

### nodejs连接mysql
下载：npm i mysql 
配置mysql的环境
```
config/db.js
//环境参数
const env = process.env.NODE_ENV

// 配置
let MYSQL_CONF

/** 
 *  通过进程获取环境变量，开发和线上不同环境到配置
 */

 //开发
if (env === 'dev') {
   // mysql
    MYSQL_CONF = {
		host: '127.0.0.1',
		user: 'root',
		password: '',
		port: '3306',
		database: 'myblog'
	} 
}

//线上
if (env === 'production') {
    // mysql
	MYSQL_CONF = {
		host: '127.0.0.1',
		user: 'root',
		password: '',
		port: '3306',
		database: 'myblog'
	}
}

module.exports = {
    MYSQL_CONF,
}
```
对mysql进行promise封装处理
```
mysql.js

const mysql = require('mysql');
const { MYSQL_CONF }  = require('../config/db');

const con = mysql.createConnection(MYSQL_CONF)

//开始连接
con.connect()

//统一执行sql的函数
function exec(sql) {
    const promise = new Promise((resolve,reject) => {
        con.query(sql,(err,result) => {
            if (err) {
                reject(err)
                return
            }
            resolve(result)
        })
    })

    return promise
}

module.exports = {
    exec
}
```

### cookie和session
#### 什么是cookie
1. 存储在浏览器的一段字符串（最大5kb）
2. 跨域不共享
3. 格式如k1=v1，因此可以存储结构化数据
4. 每次发送http请求，会将请求域的cookie一起发送给server
5. server可以修改cookie并返回给浏览器
6. 浏览器中也可以通过js修改cookie（有限制）

#### js操作cookie，浏览器中查看cookie
1. Network的Request Headers
2. Application
3. document.cookie

#### serve端操作cookie，实现登录验证
1. 查看cookie
2. 修改cookie
3. 实现登录验证

#### session的问题
1. 进程内存有限，访问量过大，内存暴增怎么办？
2. 正式线上是多进程，进程之间内存无法共享

#### redis
1. web server最常用的缓存数据库，数据存放在内存中
2. 相比于mysql，访问速度快（内存和硬盘不是一个数量级的）
3. 但是成本更高，可存储的数据量更小（内存的硬伤）
```
下载：brew install redis
启动：redis-server
主机域和端口：redis-cli
设置redis：set name value
获取redis：get name
查看redis：keys *
删除redis：del name
```


### nginx反向代理
> 用到nginx做代理，让前后端同域
#### 介绍
1. 高性能的web服务器，开源免费
2. 一般用于做静态服务，负载均衡
3. 反向代理

#### 安装配置
1. Mac: brew install nginx
2. Mac: /usr/local/etc/nginx/nginx.conf
3. 测试配置文件格式是否正确 nginx -t
4. 启动 nginx，重启 nginx -s reload
5. 停止 nginx -s stop
```
//打开配置文件
sudo vi /usr/local/etc/nginx/nginx.conf

//nginx反向代理的配置，本地服务
location / {
    proxy_pass http://localhost:8081;
}

//nginx反向代理的配置，接口地址
location /api/ {
    proxy_pass http://localhost:8000;
    proxy_set_header Host $host;
}
```

### 安全
1. sql注入：窃取数据库内容
2. XSS攻击：窃取前端的cookie内容
3. 密码加密：保障用户信息安全
