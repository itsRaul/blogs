---
title: 搭建个人博客
date: 2018-09-30 21:45:40
tags: ['github']
categories: 随笔
---

### hexo + github 搭建属于自己的个人博客
<!-- more -->

### 准备工作

1.[node](https://nodejs.org/zh-cn/)：安装node，到官网下载，一路安装即可，在命令行输入node -v，出现版本号安装成功。

2.[git](https://git-scm.com/)：一路安装即可，[教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

3.[github](https://github.com/)：程序员的交流网站，用来做博客的远程创库、域名、服务器。

(Mac系统下载Homebrew，直接在终端进行下载，brew install git、brew install node)

### 安装hexo框架

``` bash
npm install -g hexo
```

### 初始化项目
终端cd到一个选定的目录文件下(如新建一个文件夹blog)
``` bash
hexo init
```
查看blog文件下是否存在node_modules文件夹,不在则执行如下命令
``` bash
hexo install
```
开启hexo服务器，在浏览器打开网址http://localhost:4000，出现hexo页面
```bash
hexo s
```

### 创建github仓库

1.打开github，注册账号，需要和电脑关联，在本地创建ssh key，可在百度输入github与本地电脑关联

2.创建github仓库,点击+号(New repository),新建仓库，名为用户名.github.io固定写法。(用户名就是自己github的名字)

3.Create repository创建仓库，其它不用选择

4.cd到blog文件夹，打开配置文件_config.yml
```bash
deploy:
    type: git
    repository: https://github.com/xxx/xxx.github.io.git
    branch: master
```
你需要将repository后xxx换成你自己的用户名,浏览器输入xxx.github.io

### 发布文章
cd到blog文件夹，执行如下命令新建文章
```bash
hexo new "postName"
```
名为postName的文件夹在/blog/source/_posts下。

编辑文章完成，终端cd到blog文件夹下，执行如下命令
```bash
hexo g
```
```bash
hexo d
```

### 安装theme
可以去搜索关于hexo的主题页，修饰自己的个人网站,在github上Clone or download地址
终端cd到blog的目录下执行如下命令
```bash
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
cd到blog文件夹(每次部署文章的步骤)
```bash
hexo clean
```
```bash
hexo g
```
```bash
hexo d
```
至于更改theme内容，配置主题的网站，去修改blog/_config.yml文件和blog/themes/next/_config.yml，不要忘记冒号：加空格

### 绑定个人域名
1.想让自己的网站看起来更有个性，可以去万网购买喜欢的域名~~

2.购买域名后，需要进行域名解析，之前先进行域名的实名验证，填写好信息，万网有详细的教程

3.在blog/source建立CNAME的文件，没有后缀名，内容输入购买的域名，如www.xxx.com

4.在万网对域名进行解析，进入解析设置，点击添加记录，需要添加4条记录
```bash
第一条记录：
记录类型：A
主机记录:www
解析线路：默认
记录值192.30.252.154
```
```
第二条记录：
记录类型：A
主机记录:@
解析线路：默认
记录值192.30.252.154
```
```
第三条记录：
记录类型：A
主机记录:www
解析线路：默认
记录值192.30.252.153
```
```
第四条记录：
记录类型：A
主机记录:@
解析线路：默认
记录值192.30.252.153
```

### 总结和说明
hexo + github搭建个人网站非常简单，主要在域名解析部分，当时各种解析添加记录都无法进行解析，这4条记录一定得添加好，绑定属于自己的域名。
搭建属于自己的个人网站成就感满满，也希望自己一直坚持写博客，把自己遇到的问题和所做的收获一点一滴记录起来。明天是国庆节，国庆节快乐！！！








    
