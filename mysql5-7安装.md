title: mysql5.7安装
date: 2016-09-05 17:29:03
categories: [mysql]
tags:
---
#### mysql5.7 安装

```
useradd -m mysql -s /bin/bash
cd /usr/local
tar zxvf mysql-5.7.14-linux-glibc2.5-x86_64.tar.gz
mv mysql-5.7.14-linux-glibc2.5-x86_64 mysql
mkdir -p /home/data/mysql/data
chown -R mysql:mysql mysql
chown -R mysql:mysql /home/data/mysql/data

cd mysql

./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/home/data/mysql/data --initialize-insecure --socket=/usr/local/mysql/mysql.sock
```
> --initialize-insecure mysql5.7默认会生成随机密码，此选项密码设置默认密码为空

vim /etc/my.cnf
```
[client]
port=3306
socket=/usr/local/mysql/mysql.sock

[mysqld]
datadir=/home/data/mysql/data
socket=/usr/local/mysql/mysql.sock
user=mysql

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

添加mysqld服务
```
vim support-files/mysql.server
basedir=/usr/local/mysql
datadir=/home/data/mysql/data

cp support-files/mysql.server /etc/init.d/mysqld
chmod 700 /etc/init.d/mysqld
chkconfig mysqld on
```

启动服务
```
service mysqld start
```

 * 授权其他客户端连接
```
grant all privileges on *.* to root@'%' identified by 'root';
flush privileges;
```
 * 修改root密码
 ```
 update mysql.user set authentication_string=password('root') where user='root' and host='localhost'
 flush privileges;
 ```
mysql5.7已经取出password字段，改为authentication_string。
注意
1. /usr/local/mysql下确定sock没有被锁，没有mysql.sock.lock文件
2. 若第一次初始化失败，需要删除datadir目录。eg ```rm -fr /home/data/mysql/data/* ```
3. 如果客户端连接失败，检查防火墙是否关闭
```
service iptables stop    临时关闭
chkconfig iptables off   永久关闭
```


