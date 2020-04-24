---
title: nodejs开发简易版微博
date: 2020-04-24 16:36:09
tags: ['Node.js']
categories: Node.js
---

### nodejs开发简易版微博
<!-- more -->

### 创建 Koa2 项目

1.项目
```
koa2 -e name (-e用ejs作为模版引擎)

npm i 

npm run dev
```

2.环境变量，更新内容不需要重启服务
```
npm i cross-env

"dev": "cross-env NODE_ENV=dev ./node_modules/.bin/nodemon bin/www",

"prd": "cross-env NODE_ENV=production pm2 start bin/www",
```

### ejs的使用

```
引入title参数

<p>EJS Welcome to <%= title %></p>
```

```
引入ejs组件

<%- include('./user-info',{
    isMe
}) %>
```

```
if 判断

<div>
    <% if (isMe) { %>
        <p>真</p>
    <% } else { %>
        <p>假</p>
    <% } %>
</div>
```

```
循环

<ul>
    <% blogList.forEach(blog => { %>
        <li> <%= blog%> </li>
    <% }) %>
</ul>

<script>
    console.log('100')
</sctipt>
```

### mysql

#### 查询语句

```
1.增
insert into users(username,`password`,nickname) values('list','123','李四');
password是关键字

2.查
select * from users; 查询整个表

select username,nickname from users where username='lisi' and password='123'; 关键字段查询

select * from blogs order by id desc; 倒序查询

select count(id) as `count` from blogs; 查询总数，*号查询所有的列

select * from blogs order by id desc limit 3; 倒序查询blog表的3条数据

select * from blogs order by id desc limit 2 offset 2; 一页2条数据，这是第二页

select blogs.*, users.username,users.nickname from blogs inner join users on users.id = blogs.userid; 查询blogs和users两个表的数据

select blogs.*, users.username,users.nickname from blogs inner join users on users.id = blogs.userid whers users.username = 'lisi'; 查询关联lisi的数据

3.更新
update blogs set content='内容1111' where id=2;

4.删
delete from blogs where id=4;
```

#### 连接mysql

```
seq.js

const Sequelize = require('sequelize')

const conf = {
    host: 'localhost',
    dialect: 'mysql'
}

const seq = new Sequelize('koa2_weibo_db','root','',conf)

// seq.authenticate().then(() => {
//     console.log('ok')
// }).catch(() => {
//     console.log('err')
// })

module.exports = seq
```

#### 创建模型

```
const Sequelize = require('sequelize')
const seq = require('./seq')

//创建 User 模型，数据表的名字是users
const User = seq.define('user',{
    //id 会自动创建，并设为主键、自增
    userName: {
        type: Sequelize.STRING, //varchar(255)
        allowNull: false //不能为空
    },
    password: {
        type: Sequelize.STRING,
        allowNull: false
    },
    nickName: {
        type: Sequelize.STRING,
        comment: '昵称' //注释
    }
})

const Blog = seq.define('blog',{
    //id 会自动创建，并设为主键、自增
    title: {
        type: Sequelize.STRING, //varchar(255)
        allowNull: false //不能为空
    },
    content: {
        type: Sequelize.STRING,
        allowNull: false
    },
    userId: {
        type: Sequelize.INTEGER,
        allowNull: false
    }
})

// 外键关联
Blog.belongsTo(User, {
    //创建外键 Blog.userId => User.id
    foreignKey: 'userId'
})
User.hasMany(Blog, {
    foreignKey: 'userId'
})

module.exports = {
    User,
    Blog
}
```

#### 添加数据

```
const { User, Blog } = require('./model')

!(async function() {

    //创建用户
    const zhangsan = await User.create({
        userName: 'zhangsan',
        password: '123',
        nickName: '张三',
    })

    console.log('zhangsan', zhangsan.dataValues)
})()
```

#### 查找数据

```
const { Bolg, User } = require('./model')

!(async function () {

    const zhangsan = await User.findOne({
        where: {
            userName: 'zhangsan'
        }
    })
    console.log('zhangsan',zhangsan.dataValues)
})()
```

### nodejs 调试

```
 "dev": "cross-env NODE_ENV=dev ./node_modules/.bin/nodemon --inspect=9229 bin/www"

 chrome://inspect/#devices

 点击inspect
```

### jwt

1. jwt - json web token 
2. 用户认证成功之后，server 端返回一个加密的token给客户端
3. 客户端后续每次请求都带token,以示当前用户的身份

```
app.js

npm i koa-jwt jsonwebtoken

app.use(jwtKoa({
	secret: SECRET, //加密规则
}).unless({
	path: [/^\/users\/login/] //自定义哪些目录忽略 jwt 验证
}))
```

```
const jwt = require('jsonwebtoken')
const util = require('util')
const verify = util.promisify(jwt.verify)

模拟登录接口 SECRET加密规则
let token 
if (userInfo) {
    token = jwt.sign(userInfo, SECRET, {expiresIn: '1h'})
}

模拟个人信息接口，解密个人信息
router.get('/getUserInfo',async (ctx,next) => {
	const token = ctx.header.authorization
	try {
		const payload = await verify(token.split(' ')[1],SECRET)
		ctx.body = {
			code:0,
			data: {
				userInfo:payload
			},
			msg: '成功'
		}
	} catch (error) {
		ctx.body = {
			code: -1,
			msg: 'failed'
		}
	}
})
```

```
前端头部携带token
Bearer + 空格 + 服务端返回的token
Authorization Bearer token
```

### ajv 数据校验规则

```
npm i ajv

_validate.js

const Ajv = require('ajv')
const ajv = new Ajv({
    // allErrors: true // 输出所有的错误（比较慢）
})

/**
 * json schema 校验
 * @param {Object} schema json schema 规则
 * @param {Object} data 待校验的数据
 */
function validate(schema, data = {}) {
    const valid = ajv.validate(schema, data)
    if (!valid) {
        return ajv.errors[0]
    }
}

module.exports = validate

```

```
user.js

const validate = require('./_validate')

// 校验规则
const SCHEMA = {
    type: 'object',
    properties: {
        userName: {
            type: 'string',
            pattern: '^[a-zA-Z][a-zA-Z0-9_]+$', // 字母开头，字母数字下划线
            maxLength: 255,
            minLength: 2
        },
        password: {
            type: 'string',
            maxLength: 255,
            minLength: 3
        },
        newPassword: {
            type: 'string',
            maxLength: 255,
            minLength: 3
        },
        nickName: {
            type: 'string',
            maxLength: 255
        },
        picture: {
            type: 'string',
            maxLength: 255
        },
        city: {
            type: 'string',
            maxLength: 255,
            minLength: 2
        },
        gender: {
            type: 'number',
            minimum: 1,
            maximum: 3
        }
    }
}

/**
 * 校验用户数据格式
 * @param {Object} data 用户数据
 */
function userValidate(data = {}) {
    return validate(SCHEMA, data)
}

module.exports = userValidate
```

```
中间件函数

function genValidator(validatorFn) {
    async function validator(ctx,next) {
        const data = ctx.request.body
        const error = validatorFn(data)
        if (error) {
            ctx.body = new ErrorModel(jsonSchemaFileInfo)
            return
        }
        await next()
    } 
    return  validator
}

module.exports = {
    genValidator
}
```

### 图片上传

```
npm i formidable-upload-koa fs-extra --save

const koaFrom = require('formidable-upload-koa')

1.上传图片,koaFrom作为一个中间件，返回图片的大小，路径，名字，type
2.限制图片大小，大于删除图片
3.防止图片重命名字
4.创建存放文件的目录
5.移动默认的目录到新创建的目录
6.每次启动项目，判断图片存放的目录是否存在
router.post('/upload', loginCheck, koaFrom(), async (ctx,next) => {
    const file = ctx.req.files['file']
    const { size, path, name, type } = file
    ctx.body = await saveFile({
        size, 
        name, 
        type,
        filePath: path 
    })
})

const path = require('path')
const fse = require('fs-extra')

//存放图片的目录
const DIST_FOLDER_PATH = path.join(__dirname, '..', '..', 'uploadFiles')
// 文件最大体积
const MIX_SIZE = 1024 * 1024 * 1024

//是否需要创建目录
fse.pathExists(DIST_FOLDER_PATH).then(exist => {
    if (!exist) {
        fse.ensureDir(DIST_FOLDER_PATH)
    }
})

/**
 * 保存文件
 * @param {number} size 文件名
 * @param {string} filePath 文件类型
 * @param {string} name 文件体积大小 
 * @param {string} type 文件路径 
 */
async function saveFile({ size, filePath, name, type }) {
    if (size > MIX_SIZE) {
        await fse.remove(path)
        return new ErrorModel(uploadFileSizeFailInfo)
    }

    //移动文件
    const fileName = Date.now() + '.' + name
    const distFilePath = path.join(filePath, DIST_FOLDER_PATH)
    await fse.move(filePath, distFilePath)
    return new SuccessModel({
        url: '/' + fileName
    })
}

app.js
app.use(koaStatic(path.join(__dirname, '..','uploadFiles')))
```

### xss过滤

```
安装
npm i xss --save

引入
const xss = require('xss')
xss(content)
```

### PM2

#### PM2介绍
nodejs进程管理工具，可以利用它来简化很多node应用管理的繁琐任务，如性能监控、自动重启、负载均衡等，而且使用非常简单。
守护进程：服务挂掉自动重启
多进程：更好的利用CPU和内存

#### PM2 配置和使用

```
查看所有进程的列表
pm2 list

重启pm2的进程
pm2 restart <App name or id>

停止进程服务
pm2 stop <App name or id>

删除进程
pmg2 delete <App name or id>

启动
pm2 start <App name or id>

查看进程信息
pm2 info <App name or id>

监控
pm2 monit www
```

更好的利用内存和CPU
进程之间无法通讯，因此 redis mysql要用统一的服务

### nginx 代理

#### nginx介绍
1.静态资源服务器
2.负载均衡
3.反向代理
