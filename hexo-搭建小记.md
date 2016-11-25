title: hexo 搭建小记
date: 2015-09-04 22:26:18
categories:
tags:
---
### hexo
#### 安装git bash、ssh 、node.js
npm install -g hexo
mkdir /g/hexo
cd /g/hexo
hexo init
hexo generate
hexo server 可以开启服务
hexo deploy

若出现deployer not found 则把type改为git
执行 npm install hexo-deployer-git --save
修改_config.yml
deploy:
  type: git
  repository: git@github.com:yuchuan512/yuchuan512.github.io.git
  branch: master
```bash
$ git clone https://github.com/A-limon/pacman.git themes/pacman
```
修改你的博客根目录下的config.yml配置文件中的theme属性，将其设置为pacman

#### 导航栏添加自定义页面
手动生成自定义页面： hexo n page "about"
编辑 hexo/source/abount/index.md
修改 themes/pacman/_config.yml    menu: 关于: /about

#### 模板修改 hexo\scaffolds 中 的 post.md
title: {{ title }}
date: {{ date }}
categories:
description:
### git操作
git config --global user.name [username]
git config --global user.email [email]
ssh-keygen -t rsa -C "yuchuan512@163.com"
把pub文件用sublime打开粘贴到github上
若提示 **Host key verification failed. ssh git@github.com 询问的时候输入yes，此时再连接即可成功。
#### 常用命令
npm install hexo -g #安装
npm update hexo -g #升级
hexo init #初始化

hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署

hexo server #Hexo 会监视文件变动并自动更新， 无须重启服务器。
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP

hexo clean #清除缓存
hexo g #生成静态网页
hexo d #开始部署

hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub

hexo new [layout] <title>
hexo new photo "My Gallery"
hexo new "Hello World" --lang tw

#### 设置文章摘要
以上是文章摘要 <!--more--> 以下是余下全文

