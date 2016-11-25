title: zookeeper安装
date: 2016-08-13 10:06:08
categories: [zookeeper]
tags:
---
zookeeper单机安装
进入zookeeper目录下的conf子目录, 创建zoo.cfg:
	tickTime: zookeeper中使用的基本时间单位, 毫秒值.
	dataDir: 数据目录. 可以是任意目录.
	dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和dataDir相同的设置.
	clientPort: 监听client连接的端口号.
server启动 bin/zkServer.sh start
client连接 bin/zkCli.sh -server localhost:4180

伪集群
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/Users/apple/zookeeper0/data
dataLogDir=/Users/apple/zookeeper0/logs
clientPort=4180
server.0=127.0.0.1:8880:7770
server.1=127.0.0.1:8881:7771
server.2=127.0.0.1:8882:7772
新增了几个参数, 其含义如下:

initLimit: zookeeper集群中的包含多台server, 其中一台为leader, 集群中其余的server为follower. initLimit参数配置初始化连接时, follower和leader之间的最长心跳时间. 此时该参数设置为5, 说明时间限制为5倍tickTime, 即5*2000=10000ms=10s.
syncLimit: 该参数配置leader和follower之间发送消息, 请求和应答的最大时间长度. 此时该参数设置为2, 说明时间限制为2倍tickTime, 即4000ms.
server.X=A:B:C 其中X是一个数字, 表示这是第几号server. A是该server所在的IP地址. B配置该server和集群中的leader交换消息所使用的端口. C配置选举leader时所使用的端口. 由于配置的是伪集群模式, 所以各个server的B, C参数必须不同.

参照zookeeper0/conf/zoo.cfg, 配置zookeeper1/conf/zoo.cfg, 和zookeeper2/conf/zoo.cfg文件. 只需更改dataDir, dataLogDir, clientPort参数即可.

在之前设置的dataDir中新建myid文件, 写入一个数字, 该数字表示这是第几号server. 该数字必须和zoo.cfg文件中的server.X中的X一一对应.
/Users/apple/zookeeper0/data/myid文件中写入0, /Users/apple/zookeeper1/data/myid文件中写入1, /Users/apple/zookeeper2/data/myid文件中写入2.

分别进入/Users/apple/zookeeper0/bin, /Users/apple/zookeeper1/bin, /Users/apple/zookeeper2/bin三个目录, 启动server.
任意选择一个server目录, 启动客户端:

集群
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/home/zookeeper/data
dataLogDir=/home/zookeeper/logs
clientPort=4180
server.43=10.1.39.43:2888:3888
server.47=10.1.39.47:2888:3888
server.48=10.1.39.48:2888:3888
因为部署在不同的机器上，因此各server的conf/zoo.cfg 文件可以完全一样
但是myid必须唯一，在dataDir目录下新建myid文件写入id号
echo "0" > dataDir/myid

