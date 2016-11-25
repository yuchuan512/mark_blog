title: mess
date: 2016-09-05 17:30:01
categories:
tags:
---
ETL extract transform load
Jetty Debug
Run -> Edit Configurations -> Maven -> clean Jetty:run-exploded  pom.xml
Content-Type:text/html   Content-Type:application/json
HandlerInterceptorAdapter -> prehandle posthandle afterCompletion
netstat -ano | find str "8080"
class*=col 所有以col开头元素
图
1. opentsdb
2. jstorm

ctrl+shift+h 格式化html/css/javascript
ctrl+alt+j  json格式化
alt+m markdown

r 读
r+ 读写
w 覆盖写
a  附加

gitbook
编写 SUMMARY.md   README.md

AOP拦截多个方法。  requestmapping @Param(value="param" required=false)

ctrl+shift+I  类定义
alt+F1
alt+9 查看那些git修改过   view-tool window
junit generator
ASM ByteCode

四种隔离级别
未提交读 提交读  可重复读  串行化

团队博客 ATA
做的东西所处的地位、重要性
基于HBase的时间序列数据库

service iptables stop
chkconfig iptables off  永久关闭防火墙

vim /etc/seliux/config   SELinux=disabled

lsb_release -a  linux版本号
jar cvf count.jar

google技巧
"" 完全匹配单词
xx -y  包含xx不包含y，减号前面有空格，后面紧跟单词
inurl:test  url里面包含test
intitle:test   titile里面包含test
allintitle:test
filetype:pdf  seo
site:http://www.baidu.com xx  站内搜索

idea连接hbase编程，需要把hbase-site.xml放入类路径下面，修改 window/system32/drivers/etc/hosts即可

sublime setting.xml
[
{ "keys": ["alt+m"], "command": "markdown_preview", "args": { "target": "browser"} },
{ "keys": ["alt+d"], "command": "run_macro_file", "args": {"file": "Packages/Default/Delete Line.sublime-macro"} },
{ "keys": ["shift+enter"], "command": "run_macro_file", "args": {"file": "Packages/Default/Add Line.sublime-macro"} }
]

problem:Warning:java: 源值1.5已过时, 将在未来所有发行版中删除
maven工程pom.xml加入
```
 	<properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
```