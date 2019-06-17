---
title: PM2
date: 2019-06-15
tags: ['Node.js']
categories: Node.js
---

### PM2
<!-- more -->

### 客户端和服务端
![image](/images/node-serve.png)

### 线上环境
1. 服务器稳定性
2. 充分利用服务器硬件资源，以便提高性能
3. 线上日志记录

#### PM2核心价值
1. 进程守护，系统崩溃自动重启
2. 启动多进程，充分利用CPU和内存
3. 自带日志记录功能

#### PM2介绍
1. 下载安装

```
全局安装
npm i pm2 -g 

查看版本
pm2 --version

查看控制台启动的列表
pm2 list

重启 使用文件名或id
pm2 resstart <AppName>/<id>

停止
pm2 stop <AppName>/<id>

删除
pm2 delete <AppName>/<id>

查看基本信息
pm2 info <AppName>/<id>

查看进程日志
pm2 log <AppName>/<id>

监控CPU和内存信息
pm2 monit <AppName>/<id>
```

2. 基本使用

```
package.json

pm2启动项目里的app.js文件，如果是express和koa2 则为bin/www
"prd": "cross-env NODE_ENV==product pm2 start app.js"
```

#### PM2进程守护和配置
1. 遇到进程崩溃，会自动重启

2. 新建PM2配置文件

```
pm2.conf.json

{
  "apps": {
    "name": "pm2-test-server", //名字随便写
    "script": "app.js", //启动文件，express是bin/www
    "watch": true, //监听文件变化，自动重启
    "ignore-watch": [
      "node_modules",
      "logs"
    ], //不需要监听的文件
    "instances": 4, //电脑的CPU
    "error_file": "logs/err.log", //错误日志的路径
    "out_file": "logs/out.log", //console.log打印
    "log-date-format": "YYYY-MM-DD HH:mm:ss", //日志每行都标志时间
  }
}

package.json
"prd": "cross-env NODE_ENV==product pm2 start pm2.conf.json"
```

#### PM2多进程
1. 为何使用多进程
单个进程的内存是受限的

2. 多进程和redis
多进程之间，内存无法共享
多进程访问一个redis，实现数据共享