title: hadoop2.6.0_hbase1.1_zookeeper3.4.6搭建
date: 2016-09-05 17:28:41
categories:
tags:
---

hbase支持结构化半结构化
不允许跨行的事务
HBase的表必须有一个列族，列族直接影响HBase数据存储的物理特性
创建表的时候必须制定一个列族
Put p = new Put(Bytes.toBytes("mark tom")) HBase中所有数据都是作为原始数据使用字节数组的形式存储


新增用户 useradd -m hadoop -s /bin/bash
vim /etc/sysconfig/network  ====》 hostname
vim /etc/hosts ip:hostName   eg ： 10.101.100.101  master

1. ssh无密码登录
rpm -qa | grep ssh
yum install openssh-clients
yum install openssh-server

cd /etc/ssh/ssh_confg
去掉端口22和protocol注释

cd /etc/ssh/sshd_config
root用户下修改 /etc/ssh/sshd_config文件（取消以下三个变量的注释）
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile  .ssh/authorized_keys
重启服务  service sshd restart

切换到hadoop，ssh localhost无密码登录
cd /home/hadoop
ssh localhost生成  .ssh
进入/home/hadoop/.ssh
创建hadoop用户秘钥，加入授权
ssh-keygen -t rsa
cat id_rsa.pub >> authorized_keys

slaver1和slaver2做类似操作，scp id_rsa.pub root@master:/home/hadoop
加入authorized_keys 中
注意 chmod 600 authorized_keys（必须设置权限，否则centos不通过）
将authorized_key传输到slaver1,slaver2，并将所属组和用户设置为hadoop

2.
service iptables stop 临时关闭防火墙
chkconfig iptables off 永久关闭防火墙

3. 安装zookeeper
确定防火墙已经关闭
vim /usr/local/zookeeper/conf/zoo.cfg
dataDir=/home/data_zoo/data
dataLogDir=/home/logs/zklog

server.1=master:2888:3888
server.2=slaver1:2888:3888
server.3=slaver2:2888:3888
对应server的myid
vim /home/data_zoo/data/myid 1
vim /home/data_zoo/data/myid 2
vim /home/data_zoo/data/myid 3
确保权限
chown -R hadoop:hadoop/usr/local/zookeeper
chown -R hadoop:hadoop /home/data_zoo
chown -R hadoop:hadoop /home/logs

vim /etc/profile
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$ZOOKEEPER_HOME/bin:$PATH
source /etc/profile

启动
zkServer.sh start
若启动有问题，使用 zkServer.sh start-foreground 查看启动过程报错信息
zkServer.sh stop
zkCli.sh -server localhost:2181
至此，zk搭建完成

4. hadoop配置
参考 http://blog.csdn.net/zhshulin/article/details/50413240
修改 slaves、core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml
注意，需要/usr/loca/hadoop/etc/hadoop 中 vim hadoop-env.sh  yarn-env.sh 配置JAVA_HOME=/opt/jdk7
出现name service not found
vim ~/.bashrc
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"



5. hbase搭建
参考 http://blog.csdn.net/andie_guo/article/details/44086433
http://blackproof.iteye.com/blog/2023798

vim regionservers === > slaver1  slaver2

vim hbase-env.sh
export JAVA_HOME=/opt/jdk7
export HBASE_HOME=/usr/local/hbase
export HADOOP_HOME=/usr/local/hadoop
export HBASE_LOG_DIR=/usr/local/hbase/logs
export HBASE_MANAGES_ZK=false  不使用hbase自带zk

vim hbase-site.xml
<property>
    <name>hbase.rootdir</name>
    <value>hdfs://master:9000/hbase</value>
</property>
<property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
</property>
<property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
</property>
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>master,slaver1,slaver2</value>
</property>
<property>
    <name>hbase.master</name>
    <value>master:6000</value>
</property>
<property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/home/data_zoo/data</value>
</property>
<property>
    <name>dfs.support.append</name>
    <value>true</value>
</property>

free 详解 http://www.cnblogs.com/coldplayerest/archive/2010/02/20/1669949.html

localhost:50070 web界面

