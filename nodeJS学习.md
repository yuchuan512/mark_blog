title: nodeJS学习
date: 2016-09-28 10:11:22
categories:
tags:
---
Node.js 是一个让javascript运行在服务端的开发平台。实现了注入文件系统、模块、包、操作系统API、网络通信等Core Javascript没有或不完善的功能。
Node.js最大特点就是采用异步式IO与事件驱动的架构设计。高并发方案，传统的架构是多线程模型，为每个业务逻辑提供一个系统线程，通过系统县城切换来弥补同步式IO调用时的事件开销。Node.js使用的是单线程模型，对于所有IO都采用异步式的请求方式，避免了频繁的上下文切换。Node.js在执行的时候会维护一个事件队列，程序在执行时进入事件循环等待下一个事件到来，每个异步式IO请求完成后会被推送到事件队列，等待程序进程进行处理。
比如数据库查询操作。进程在执行到db.query的时候不会等待结果返回，而是直接继续执行后面的语句，直到进入事件循环。当数据库查询结果返回时，会
将事件发送到事件队列，等到线程进入事件循环之后，才会调用之前的回调函数继续执行后面的逻辑。

为了解决无法利用多个核心cpu的问题，node.js提供了child_process模块，通过多进程来实现对多核CPU的利用，child_process模块提供了四个创建子进程的函数，分别是spawn,exec,execFile和fork

[安装MongoDB]
新建一个mongodb.list文件
echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/mongodb.list
重新导入本地数据库安装包
apt-get update

启动MongoDB
mongod -f /etc/mongod.conf --fork --nojournal
-f 用来制定配置文件   --fork后台启动  --nojournal关闭日志系统
不关闭日志系统可能会锁定数据库
如果MongoDB不能运行一般来说是锁定了数据库，可以删除锁文件，然后再重新启动
rm /var/lib/mongodb/mongod.lock

一般启动步骤：
```
rm /var/lib/mongodb/mongod.lock
mongod -f /etc/mongod.confg --fork --nojounal
```
db.help 查看数据库帮助信息
db.user.help   对指定数据库的集合进行操作、管理和监控
db.serverStatus()  指令来查看我们的数据库状态。
db.status()   当前数据库统计信息
db.getCollectionName()   查询指定数据库包含的集合名称列表
db.getName()  当前使用的数据库名称
db.dropDatabase()  删除数据库
db.addUser("userName","password")    为数据库添加用户
db.dropUser("userName")
db.shutdownServer()   终止数据库服务器进程  或者kill mongod进程。
如果你当前不是工作在admin数据库上，那无法终止数据库服务器进程 。使用 use admin 切换到admin上

mongostat是mongdb自带的状态检测工具
mongostat和db.serverStatus()查询的内容相仿但是db.serverStatus()内容是非实时的，mongostat的内容是实时的
它的输出有以下几列，我们可以对照看看，找找感觉：
inserts/s	每秒插入次数
query/s	每秒查询次数
update/s	每秒更新次数
delete/s	每秒删除次数
getmore/s	每秒执行getmore次数
command/s	每秒的命令数，比以上插入、查找、更新、删除的综合还多，还统计了别的命令
flushs/s	每秒执行fsync将数据写入硬盘的次数。
mapped/s	所有的被mmap的数据量，单位是MB
vsize	虚拟内存使用量，单位MB
res	物理内存使用量，单位MB
faults/s	每秒访问失败数（只有Linux有），数据被交换出物理内存，放到swap。不要超过100，否则就是机器内存太小，造成频繁swap写入。此时要升级内存或者扩展
locked %	被锁的时间百分比，尽量控制在50%以下吧
idx miss%	索引不命中所占百分比。如果太高的话就要考虑索引是不是少了
q|t|r|w	当Mongodb接收到太多的命令而数据库被锁住无法执行完成，它会将命令加入队列。这一栏显示了总共、读、写3个队列的长度，都为0的话表示mongo毫无压力。高并发时，一般队列值会升高。
conn	当前连接数
time	时间戳

profiler方式是使用Profile日志来完成数据库监控的
Profiler默认是关闭的，你可以选择全部开启，或者有慢查询的时候开启。使用setProfilingLevel命令进行开启，setProfilingLevel命令参数可以是0,1,2，其中0表示关闭profile；1表示只抓取slow查询；2表示抓取所有数据。getProfilingLeve()用来获取目前profiler的设置。
查看Profiling数据,可以使用show profile  ``` show profile  ```

MongoDB的web端口在Mongodb数据库服务器端口的基础上加1000。默认MongoDB端口 27017  web端口为28017



1. 连接读取mongodb
```
var mongodb = require('mongodb');
var server = new mongodb.Server("10.13.32.55",27017,{safe:true});
var db = new mongodb.Db("hello",server,{});
db.open(function (error, db) {
    if(error) throw error;
    db.collection("col",function (error, collection) {
        collection.findOne({
            name:"bird"
        },function (err, name) {
            console.log(name);
            db.close();
        });
    });
});
```

2.  process
```
var os = require("os");
process.stdin.on("readable", function () {
    var chunk = process.stdin.read();
    if(chunk!=null){
        process.stdout.write("data:" + chunk);
        process.exit(0);
    }
});
exit事件：
进程正常退出之前，会触发exit事件
process.on("exit", function (code) {
    console.log("I am tired");
});

uncaughtException事件
进程异常退出时会触发uncaughtException事件
process.on("uncaughtException", function (err) {
    console.log(err);
    console.log(err.stack);
});
throw new Error("exception ");

设置编码
process.stdin.setEncoding(编码);
process.stdout.setEncoding(编码);
process.stderr.setEncoding(编码);


```
3. 读写文件
```
var fs = require("fs");
fs.writeFile("test.txt","hello world",function (err) {
    if(err) throw err;
    console.log("saved successfully");

})

追加文件
fs.appendFile("test.txt","append ",function(err){

});



```


