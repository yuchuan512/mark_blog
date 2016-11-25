title: shadowSocks安装
date: 2016-06-09 22:30:38
categories: [tool,shadowsocks]
tags:
---
1. 创建秘钥
其中private部分存储在你的设备本地，而public部分则需要上传到远程设备上

ssh-keygen -t rsa -C "yuchuan512@163.com"  添加密钥

2. 安装shadowsocks
首先是安装一些必要的组件：在服务端输入以下指令：
yum install m2crypto python-setuptools
easy_install pip
pip install shadowsocks

建立配置文件
/etc/shadowsocks.json
{
"server":"2604:a880:1:20::74f:8001",
"server_port":"8388",
"local_address":"127.0.0.1",
"local_port":"1080",
"password":"yuchuan78",
"method":"aes-256-cfb",
"fast_open":"false",
"workers":"1"
}

启动SS
ssserver -c /etc/shadowsocks.json

### 后台运行shadowsocks
如果你想在后台运行 Shadowsocks，可以使用 supervisor

apt-get update
apt-get install supervisor

![后台运行 ](https://github.com/clowwindy/shadowsocks/wiki/%E7%94%A8-Supervisor-%E8%BF%90%E8%A1%8C-Shadowsocks/a3a5d48dbe0e82a7b9b0836c3678230c8be457bd)
echo_supervisord_conf > /etc/supervisord.conf

编辑配置文件supervisord.conf，在后面添加

[program:shadowsocks]
command=ssserver -c /etc/shadowsocks.json
autostart=true
autorestart=true
user=nobody

启动
默认路径配置启动：supervisord
指定路径配置启动：supervisord -c "指定路径"/supervisord.conf

常用命令
获得所有程序状态 supervisorctl status
关闭目标程序 supervisorctl stop ssserver[shadowsocks]
启动目标程序 supervisorctl start ssserver[shadowsocks]
关闭所有程序 supervisorctl shutdown

可以检查日志或者控制 Shadowsocks 的运行：
supervisorctl tail -f shadowsocks stderr
supervisorctl restart shadowsocks

### finalspeed
一键安装之后
chmod+  +X /etc/rc.local 编辑 /etc/rc.loal ，加入 /fs/start.sh
设置定时重启，防止撑破内存
crontab -e 加入
0 1 * * * sh /fs/restart.sh，定时凌晨1点重启释放缓存
常用命令
启动   sh /fs/start.sh
停止   sh /fs/stop.sh
重启   sh /fs/restart.sh
卸载   sh /fs/stop.sh   rm -fr /fs
默认会占用150端口，若端口已经被占用，需重新指定端口
mkdir -p /fs/cnf; echo 端口号 > /fs/cnf/listen_port; sh /fs/restart.sh
查看日志文件   tail -f  /fs/server.log





** 若出现端口8388连接不上，则把shadowsocks.json中的 server改为 "0.0.0.0" **

### Chrome工具
SwitchyOmega.crx  自由切换
OmegaOptions.bak  规则备份

使用Proxifier设置全局代理


### 安装Pip
Centos 下载编译安装 pip
wget --no-check-certificate https://github.com/pypa/pip/archive/1.5.5.tar.gz
tar zvxf 1.5.5.tar.gz    #解压文件
cd pip-1.5.5/
python setup.py install

### 升级python
https://www.python.org/ftp/python/3.4/Python-3.4.tar.bz2
解压
tar -jxvf Python-2.7.3.tar.bz2
进入目录，编译安装
#./configure
#make all
#make install
#make clean
#make distclean
建立软连接，使系统默认的python指向python2.7
#mv /usr/bin/python /usr/bin/python2.6.6
#ln -s /usr/local/bin/python2.7 /usr/bin/python


https://www.python.org/ftp/python/3.4.2/Python-3.4.2.tgz

