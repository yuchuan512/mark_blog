title: mysql绿色版安装
date: 2016-10-23 19:18:09
categories:
tags:
---
window mysql绿色版安装
1. my.ini，将配置文件放在c:\window目录下
```
[client]
port=3306
default-character-set=utf8

[mysqld]
basedir = F:\green\mysql64
datadir = F:\green\mysql64\data
port = 3306
character_set_server=utf8
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
```
2. 添加环境变量
变量名：MYSQL_HOME
变量值：D:\Program Files\mysql-5.6.14-winx64
即为mysql的自定义解压目录。
再在Path中添加  %MYSQL_HOME%\bin

3. 安装注册
mysqld -install
如果出现Install/Remove of the Service Denied! 切换为管理员进行操作
移除服务命令为：mysqld remove

4. 启动mysql服务
net start mysql

5. 修改root密码
use mysql
update user set password=PASSWORD("888999") where user='root'
flush privileges

