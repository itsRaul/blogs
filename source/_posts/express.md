---
title: express
date: 2019-06-13 18:18:51
tags: ['Node.js']
categories: Node.js
---

### express使用
<!-- more -->

### 安装express
1. npm install express-generator -g
2. express express-test
3. npm install
4. npm start

### 处理路由
app.js引入并注册路由，根路由和子路由进行拼接
```
var express = require('express');
var router = express.Router();

router.get('/list', function(req, res, next) {
  //res.json直接返回json，还可以设置头部是json
  res.json({
    errno: 0,
    data: [1,2,3]
  })
});
module.exports = router;
```
```
var express = require('express');
var router = express.Router();

router.post('/login', function(req, res, next) {
  const { username,password } = req.body
  res.json({
    errno: 0,
    data: {
      username,
      password
    }
  })
});

module.exports = router;
```

### session和redis
1. 配置redis
2. connect-redis将session相关信息持久化
3. 配置session

```
const expressSession = require('express-session');
const RedisStore = require('connect-redis')(expressSession)

const redisClient = require('./db/redis')
const sessionStore = new RedisStore({
  client: redisClient
})

app.use(expressSession({
  name: 'session-name', // 这里是cookie的name，默认是connect.sid
  secret: 'my_session_secret', // 建议使用 128 个字符的随机字符串
  store: sessionStore,
  cookie: {
    path: '/', //默认配置，根目录，前端每个路由都可以用到
    resave: true,
    saveUninitialized: false,
    httpOnly: true, //默认配置,前端无法修改cookie
    maxAge: 24*60*60*1000 //传入时间段，24小时失效
  }
}))
```

### 中间件
1. 中间件(middleware)是一个函数,他可以访问请求对象（request object(req)）,响应对象（response object(res)）和web应用中处于请求-响应循环
2. app.use()就是通常所说的使用中间件

```
function middleware(req,res,next){
  // 做该干的事

  // 做完后调用下一个函数
  next();
}
```
next()表示函数数组中的下一个函数

### 中间件原理
1. 创建个实例，app.use用来注册中间件，先收集起来
2. 遇到http请求，根据path和method判断触发哪些
3. 实现next机制，即上一个通过next触发下一个

```
link-express.js

const http = require('http')
const slice = Array.prototype.slice

/**  
 * 定义LikeExpress的class,存放中间件的变量
 * use，get，post都是中间件函数，统一放在register()处理，结果返回分别存入不同的数组
 * register()判断第一个参数，如果是字符串，说明是一个路由。不是字符串则为根路由。把path、stack放在info中，stack是一个数组
 * listen()创建server对象去监听，增加一个callback函数
 * callback函数返回一个回调函数，定义res.json函数设置类型，返回JSON字符串
 * 获取url,method。通过match函数去匹配中间件列表
 * match函数通过routes.all和method把可用的中间件找出来，放在stack中
 * 处理next机制
 * 
*/

class LikeExpress {
  constructor() {
    //存放中间件列表
    this.routes = {
      all: [], //存放app.use(...)的中间件
      get: [],
      post: []
    }
  }

  register(path) {
    const info = {}
    
    /**
     * 传人的第一个参数可能是路由或一个中间件
     * 如果是路由，后面则有多个中间件
     * 如果是中间件,则第一个参数默认为根路径 
     */ 
    if (typeof path === 'string') {
      info.path = path
      //从第二个参数开始，转换为数组，存入stack
      info.stack = slice.call(arguments,1)
    }else {
      info.path = '/'
      //从第一个参数开始，转换为数组，存入stack
      info.stack = slice.call(arguments,0)
    }
    return info
  }

  //定义函数，表层输出的方法
  use() {
    //通过apply把当前函数的参数传入register(),并执行register()，返回info
    const info = this.register.apply(this,arguments)
    this.routes.all.push(info)
  }

  get() {
    const info = this.register.apply(this,arguments)
    this.routes.get.push(info)
  }

  post() {
    const info = this.register.apply(this,arguments)
    this.routes.post.push(info)
  }

  match(method,url) {
    let stack = []
    if (url === '/favicon.ico') {
      return stack
    }

    //获取routes
    let curRoutes = []
    curRoutes = curRoutes.concat(this.routes.all)
    curRoutes = curRoutes.concat(this.routes[method])

    curRoutes.forEach(routeInfo => {
      if (url.indexOf(routeInfo.path === 0)) {
        stack = stack.concat(routeInfo.stack)
      }
    })
    return stack
  }

  //核心的next机制
  handle(req,res,stack) {
    const next = () => {
      //拿到第一个匹配的中间件
      const middleware = stack.shift()
      if (middleware) {
        //执行中间件函数
        middleware(req,res,next)
      }
    }
  }

  callback() {
    return (req,res) => {
      res.json = (data) => {
        res.setHeader('Content-type','application/json')
        res.end(JSON.stringify(data))
      }
      const url = req.url
      const method = req.method.toLowerCase()

      const resultList = this.match(method,url)
      this.handle(req,res,resultList)
    }
  }

  listen(...args) {
    const server = http.createServer(this.callback())
    server.listen(...args)
  }
}

/**
 * 输出工厂函数
 */
module.exports = () => {
  return new LikeExpress()
}
```

### 日志-morgan
新建logs文件夹下的access.log
判断不同的环境

```
const ENV = process.env.NODE_ENV
if (ENV !== 'production') {
  app.use(logger('dev'))
} else {
  //线上环境
  const logFileName = path.join(__dirname,'logs','access.log')
  const writeStream = fs.createWriteStream(logFileName,{
    flags: 'a'
  })
  app.use(logger('combined',{
    stream: writeStream
  }))
}
```