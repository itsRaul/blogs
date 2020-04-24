---
title: 阿里云部署nodejs
date: 2020-04-24 16:38:37
tags: ['Node.js']
categories: Node.js
---

### 阿里云部署nodejs
<!-- more -->

### 购买阿里云服务器

```
启动
ssh root@ip
```
```
退出
exit
```

### 安装nodejs

```
安装环境之前先更新一下已有的包

yum update
```

```
使用yum安装指定版本node

curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -

yum install -y nodejs

node -v

npm -v

npm i nvm pm2 -g
```

### 安装MySQL

```
安装

yum install mysql
yum install mysql-server
yum install mysql-devel

```

```
修改mysql密码并配置远程连接
mysqladmin -u root password '密码'  这里我暂时用root
使用命令进入数据库：
mysql -uroot -proot
```

```
解决mysql“Access denied for user 'root'@'IP地址'
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```

```
添加安全组规则

类型： MySQL(3306)
优先级： 100
授权类型： 地址段访问
授权对象: 0.0.0.0/0
```

### 安装nginx 

```
yum install nginx

nginx -s reload
```

```
解决nginx 启动问题
vim /etc/nginx/conf.d/default.conf

listen       80 default_server;
listen       [::]:80 default_server;

修改为:
listen       80;
#listen       [::]:80 default_server;
```

```
添加安全组

类型： HTTP（80）
优先级： 100
授权类型： 地址段访问
授权对象: 0.0.0.0/0
```

### 部署nodejs项目

```
下载git 
yum install git

git --version #查看版本
```

```
根目录建立service文件夹
mkdir service 

进入service
git clone https://github.com/itsRaul/koa2-weibo-code.git

下载
npm i

然后以pm2启动项目
npm run prd

pm2 list # 列表 PM2 启动的所有的应用程序

pm2 reload all # 重启 cluster mode下的所有应用

```

```
安全组配置
类型： TCP（80）
端口范围: 3000/3000
优先级： 100
授权类型： 地址段访问
授权对象: 0.0.0.0/0
```

### 安装redis
```
1. 设置Redis的仓库地址
yum install epel-release

2. 安装redis
yum install redis

3.默认安装目录：配置文件在/etc/redis.conf；服务器和客户端在 /usr/bin/redis-server     redis-cli
whereis redis //可以查看配置文件在哪

4.修改配置文件vim /etc/redis.conf  
进入redis.conf shift+i可以修改文件  :wq保存修改

#监听所有的IP地址（61行）
bind 127.0.0.1注释掉为#bind 127.0.0.1
#设置为守护进程（128行）
daemonize no 改为 daemonize yes 
#设置密码（480行）不用设置密码
requirepass xxx
#最大物理内存设置（537行）在真实环境必须部署，否则物理内存会被耗尽。一般配置200mb/500mb/1gb/2gb。配置了最大内存 maxmemory 之后记得配置过期删除策略
maxmemory 200mb
#过期删除策略（560行）volatile-lru -> 根据LRU算法生成的过期时间来删除。 # allkeys-lru -> 根据LRU算法删除任何key。 # volatile-random -> 根据过期设置来随机删除key。 # allkeys->random -> 无差别随机删。 # volatile-ttl -> 根据最近过期时间来删除（辅以TTL） # noeviction -> 谁也不删，直接在写操作时返回错误。
maxmemory-ploicy volatile-lru

5.启动redis
service redis start 
如果需要设置开机自动启动
chkconfig redis on 

6.查看版本
redis-server -v

7.测试启动
redis-cli ping
返回PONG，启动成功

8.redis-cli 启动redis客户端
#无密码 
redis-cli -h 主机ip -p 端口
#有密码
redis-cli -h 主机ip -p 端口 -a 密码 
　或 先redis-cli,之后输入auth+空格+密码

https://www.cnblogs.com/kingsonfu/p/9819657.html
```

### 部署react 项目

```
1. 建立website 文件
2. 拉取项目
3. 在/etc/nginx/conf.d目录下新建配置文件
```
touch demo_client.conf 
vim /etc/nginx/conf.d/demo_client.conf

server {
        listen 8080;
        root    /root/website/demo-client/build;
        index   index.html index.html;
        location / {
         try_files $uri $uri/ /index.html;
        }
}

:wq
```

解决nginx 500  是因为用户权限不一致
vim /etc/nginx/nginx.conf
use nginx 改成 use root 
```