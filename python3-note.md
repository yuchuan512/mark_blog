title: python3_note
date: 2016-10-21 11:29:48
categories: [python3]
tags: [python3]
---
### python3.4基础
print(10/3)    浮点除   3.3333333333333335
print(10//3)   地板除   3
在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。
用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件：
Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符：
```ord('A')   chr(66)```
含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错。
```
len(b'ABC')
3
len('中文'.encode('utf-8'))
6
```
1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节。
utf-8编码是变长的
以utf-8编码
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```
保留两位小数   '%.2f' % 3.142592653

如果没有return语句，函数执行完毕后也会返回结果，只是结果为None。
return None可以简写为return。
默认参数必须指向不变对象！
获取当前目录包含的文件
import os
L = [d for d in os.listdir('.')]

d = {'x': 'A', 'y': 'B', 'z': 'C'}
for k, v in d.items():
    print(k, '=', v)

print([k + '=' + v for k, v in d.items()])

生成器
第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator：
第二种方法：如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator
变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

可以直接作用于for循环的数据类型有
1. 集合数据类型，list,tuple,dict,set,str等
2. generator，包括生成器和带yield的generator function
这些可以直接作用于for循环的对象统称为可迭代对象，Iterable
可以使用 isinstance()判断一个对象是否是Iterable对象
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。

生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator
把list、dict、str等Iterable变成Iterator可以使用iter()函数：
isinstance(iter([]), Iterator)
isinstance(iter('abc'), Iterator)

凡是可作用于for循环的对象都是Iterable类型；
凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；
集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

字符串转整数（利用高阶函数）
```
def str2int(s):
    def fn(x, y):
        return 10 * x + y

    def char2num(s):
        return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]

    return reduce(fn, map(char2num, s))

print(str2int('153'))
```
和map()类似，filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

返回函数
```
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
```
理解闭包
但一个函数返回了一个函数后，其内部的局部变量还被新函数引用
所以： 返回函数不要引用任何循环变量，或者后续会发生变化的变量。
```
def count():
    fs = []
    for i in range(1, 4):
        def f():
            return i * i
        fs.append(f)
    return fs

f1, f2, f3 = count();
print(f1())
print(f2())
print(f3())
```
输出均为9，因为i值变为了3，所以的返回函数引用的都是这个变量。

匿名函数
lambda x: x * x 相当于
def f(x):
	return x * x
可以把匿名函数赋给一个变量
f = lambda x: x * x
print(f(5))

装饰器
def log2(func):
    def wrapper(*args, **kw):
        print("call %s()" % func.__name__)
        return func(*args, **kw)
    return wrapper

@log2
def now():
    print('2015-03-25')


def log3(text):
    def decorator(func):
        @functools.wraps(func)
        def wrap(*args, **kw):
            print("call %s %s " % (func.__name__, text))
            return func(*args, **kw)
        return wrap
    return decorator


@log3("execute")
def now():
    print('2015-03-25')

#### 编码
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

python解释器默认会搜索当前目录、所有已安装的内置模块和第三方模块，搜索路径存放在sys模块的path变量中；
import  sys
sys.path
添加自己的搜索目录
方案1： import sys   -> sys.path.append("/Users/yuchuan/my_py_scripts")
方案2:  设置环境变量PYTHONPATH

访问属性 加上双下划线，private
捕获异常
try:
    stu.set_score(299)
except ValueError:
    stu.set_score(10)
finally:
    print("finally")

要获得一个对象的所有属性和方法，可以使用dir()函数，返回一个包含字符串的list，比如获得一个str对象的所有属性和方法
dir("ABC")
类似__xxx__的属性和方法在Python中都是有特殊用途的，比如__len__方法返回长度。在Python中，如果你调用len()函数试图获取一个对象的长度，实际上，在len()函数内部，它自动去调用该对象的__len__()方法，所以，下面的代码是等价的：
len("ABC") 与 "ABC".__len__()是等价的

实例属性和类属性

python枚举
from enum import Enum
Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
for name, member in Month.__members__.items():
    print(name, '=', member, ',', member.value)

from enum import Enum, unique
@unique
class Weekday(Enum):
    sun = 0
    mon = 1
    Tue = 2
    Wed = 3

day1 = Weekday.sun
print(day1.value)
print(Weekday.Tue.value)

使用日志和断言
import logging
logging.basicConfig(level=logging.INFO)

def foo(s):
    n = int(s)
    assert n != 0
    logging.info("n ! = 0")
    return 10 / n

可以在单元测试中编写两个特殊的setUp()和tearDown()方法。这两个方法会分别在每调用一个测试方法的前后分别被执行


单元测试可以有效地测试某个程序模块的行为，是未来重构代码的信心保证。
单元测试的测试用例要覆盖常用的输入组合、边界条件和异常。
单元测试代码要非常简单，如果测试代码太复杂，那么测试代码本身就可能有bug。
单元测试通过了并不意味着程序就没有bug了，但是不通过程序肯定有bug。

python单元测试
import unittest
from test.test2 import Dict
class TestDict(unittest.TestCase):
    def test_init(self):
        d = Dict(a=1, b='test')
        self.assertEqual(d.a, 1)
        self.assertEqual(d.b, 'test')
        self.assertTrue(isinstance(d, dict))
测试方法需要以test_开头
从unittest.TestCase继承。
以test开头的方法就是测试方法，不以test开头的方法不被认为是测试方法，测试的时候不会被执行。

setUp与tearDown
可以在单元测试中编写两个特殊的setUp()和tearDown()方法。这两个方法会分别在每调用一个测试方法的前后分别被执行。
def setUp(self):
    print("setup...")

def tearDown(self):
    print("teardown...")


json
import json

d = dict(name='Bob', age=20, score=80)
str = json.dumps(d)
print(str)

d = json.loads(str)
print(d['name'])

json和对象互相转换
import json

class Student(object):

    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score

    def get_name(self):
        return self.name

def student2dict(std):
    return {
        'name': std.name,
        'age': std.age,
        'score': std.score
    }

def dict2student(d):
    return Student(d['name'], d['age'], d['score'])

s = Student('Bob', 20, 88)
str = json.dumps(s, default=student2dict)
print(str)

stu = json.loads(str, object_hook=dict2student)
print(stu.get_name)

多线程加锁
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()


def change_it(n):
    # 先存后取，结果应该为0:
    global balance
    balance = balance + n
    balance = balance - n

def run_thread(n):
    for i in range(10):
        lock.acquire()
        try:
            change_it(n)
        finally:
            lock.release()

t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
print(balance)

threadlocal使用
import threading

local_school = threading.local()


class Student(object):
    def __init__(self, name):
        self.name = name


def process_student():
    std = local_school.student
    print("hello %s (in %s)" % (std, threading.current_thread().name))


def process_thread(name):
    local_school.student = name
    process_student()


t1 = threading.Thread(target=process_thread, args=('alice',), name="thread_A")
t2 = threading.Thread(target=process_thread, args=("bob", ), name="thread_B")
t1.start()
t2.start()
t1.join()
t2.join()

异步IO用单进程单线程模型来执行多任务，这种全新的模型称为事件驱动模型
对应到Python语言，单进程的异步编程模型称为协程，有了协程的支持，就可以基于事件驱动编写高效的多任务程序

#### 正则表达式
正则表达式中，用*表示任意个字符（包括0个），用+表示至少一个字符，用?表示0个或1个字符
m = re.match(r'(\d{3})-(\d{3,8})$', '010-12345')
print(m.group(0))  group(0)表示全部字符串
print(m.group(1))  010
print(m.group(2))  12345

t = '19:05:30'
m = re.match(r'^(0[0-9]|1[0-9]|2[0-3]|[0-9])\:(0[0-9]|1[0-9]|2[0-9]|3[0-9]|4[0-9]|5[0-9]|[0-9])\:(0[0-9]|1[0-9]|2[0-9]|3[0-9]|4[0-9]|5[0-9]|[0-9])$', t)
print(m.group(1))  ==》 19
print(m.group(2))  ==》 05
print(m.group(3))  ==》 30


正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符。加个?就可以采用非贪婪匹配：
m = re.match(r'^(\d+)(0*)$', '10230000')
print(m.groups())
('10230000', '')
在d+后面加上？表示非贪婪匹配
m = re.match(r'^(\d+?)(0*)$', '10230000')
print(m.group(1)) ==》 1023
print(m.group(2)) ==》 0000

常用內建模块
datetime
from datetime import datetime
转换时间
cday = datetime.strptime('2016-6-1 19:00:00' , '%Y-%m-%d %H:%M:%S')
创建datetime对象
time_d = datetime(2016, 10, 20, 16, 3)
print(time_d.timestamp())
t = 1476950580.0
timestamp解析datetime对象
print(datetime.fromtimestamp(t))

时间计算
from datetime import datetime, timedelta
now = datetime.now()
print(now)
print(now+timedelta(hours=10))
print(now-timedelta(days=1))
print(now+timedelta(days=2,hours=12))

collections
namedtuple  deque    defaultdict

OrderedDict


Counter
from collections import Counter
c = Counter()
for ch in 'programming':
    c[ch] = c[ch] + 1
print(c['m'])

Base64
Base64是一种用64个字符来表示任意二进制数据的方法。
import base64
print(base64.urlsafe_b64encode(b'\xb7\x1d\xfb\xef\xff'))
print(base64.urlsafe_b64decode(b'abcd--__'))

hashlib
import hashlib
md5 = hashlib.md5()
md5.update("how to use md5 in " .encode('utf-8'))
md5.update("python programming ".encode("utf-8"))
print(md5.hexdigest())

sha1 = hashlib.sha1()
sha1.update("how to use md5 in " .encode('utf-8'))
sha1.update("python programming ".encode("utf-8"))
print(sha1.hexdigest())

XML
from xml.parsers.expat import ParserCreate

class DefaultSaxHandler(object):

    def start_element(self, name, attrs):
        print('sax:start_element: %s, attrs: %s' % (name, str(attrs)))

    def end_element(self, name):
        print('sax:end_element: %s' % name)

    def char_data(self, text):
        print('sax:char_data: %s' % text)


xml = r'''<?xml version="1.0"?>
<ol>
    <li><a href="/python">Python</a></li>
    <li><a href="/ruby">Ruby</a></li>
</ol>
'''

handler = DefaultSaxHandler()
parser = ParserCreate()
parser.StartElementHandler = handler.start_element
parser.EndElementHandler = handler.end_element
parser.CharacterDataHandler = handler.char_data
parser.Parse(xml)


python连接mysql
安装包  pip install PyMySQL  [PyMySQL github](https://github.com/PyMySQL/PyMySQL)
import pymysql
try:
    conn = pymysql.connect(host='localhost', user='root', passwd='888999', db='yuchuan')
    cur = conn.cursor()
    cur.execute('select * from info')
    data = cur.fetchall()
    for d in data:
        print("ID:"+str(d[0])+' name:'+str(d[1])+" age:"+str(d[2]))
    cur.close()
    conn.close()
except Exception as e:
    print(e)

或者使用mysql官方驱动
[mysql github](http://dev.mysql.com/downloads/connector/python/)

import mysql.connector
conn = mysql.connector.connect(user='root', password='888999', database='yuchuan')
cursor = conn.cursor()
cursor.execute('select * from info')
data = cursor.fetchall()
for d in data:
    print("ID "+str(d[0])+",name "+str(d[1])+",age"+str(d[2]))
cursor.close()
conn.close()

orm 框架 ，安装 SQLAlchemy
pip install SQLAlchemy

from sqlalchemy import Column, String, create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = 'user'
    id = Column(String(20), primary_key=True)
    name = Column(String(20))


engine = create_engine('mysql+mysqlconnector://root:888999@localhost:3306/yuchuan')
DBSession = sessionmaker(bind=engine)
session = DBSession()
new_user = User(id='5', name='Bob')
session.add(new_user)
session.commit()
session.close()

#### 协程
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('Consumer %s..' % n)
        r = '200 OK'


def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n += 1
        print("Producer %s..." % n)
        r = c.send(n)
        print("produce return %s" % r)
    c.close()

c = consumer()
produce(c)
整个流程无锁，由一个线程执行，produce和consumer协作完成任务，所以称为“协程”，而非线程的抢占式多任务。

使用协程读取网页
import asyncio

@asyncio.coroutine
def wget(host):
    print('wget %s...' % host)
    connect = asyncio.open_connection(host, 80)
    reader, writer = yield from connect
    header = 'GET / HTTP/1.0\r\nHost: %s\r\n\r\n' % host
    writer.write(header.encode('utf-8'))
    yield from writer.drain()
    while True:
        line = yield from reader.readline()
        if line == b'\r\n':
            break
        print('%s header > %s' % (host, line.decode('utf-8').rstrip()))
    # Ignore the body, close the socket
    writer.close()

loop = asyncio.get_event_loop()
tasks = [wget(host) for host in ['www.sina.com.cn', 'www.sohu.com', 'www.163.com']]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()

使用web模块实现rest
```
easy_install web.py 安装web模块
import web
import xml.etree.ElementTree as ET

tree = ET.parse("user_data.xml")
root = tree.getroot()
urls = (
    '/users', 'list_users',
    '/users/(.*)', 'get_user'
)
app = web.application(urls, globals())

class list_users:
    def GET(self):
        output = 'users:['
        for child in root:
            print('child ', child.tag, child.attrib)
            output += str(child.attrib) + ','
        output += ']'
        return output


class get_user:
    def GET(self, user):
        print('user ' + user)
        for child in root:
            if child.attrib['id'] == user:
                return str(child.attrib)

if __name__ == '__main__':
    app.run()
```

Flask 框架
```
from flask import Flask

app = Flask(__name__)


@app.route("/")
def index():
    return "hello world"


if __name__ == '__main__':
    app.run(debug=False)


```

curl -i http://localhost:5000
curl -u ok:python http://localhost:5000
```
from flask import Flask, jsonify
from flask import abort
from flask import make_response
from flask import request
from flask.ext.httpauth import HTTPBasicAuth

auth = HTTPBasicAuth()
app = Flask(__name__)

tasks = [
    {
        'id': 1,
        'title': u'Buy groceries',
        'description': u'Milk, Cheese, Pizza, Fruit, Tylenol',
        'done': False
    },
    {
        'id': 2,
        'title': u'Learn Python',
        'description': u'Need to find a good Python tutorial on the web',
        'done': False
    }
]


@auth.get_password
def get_password(username):
    if username == 'ok':
        # 相当于 密码为python
        return 'python'
    return None


@auth.error_handler
def unauthorized():
    return make_response(jsonify({'error': 'Unauthorized access'}), 401)


@app.route("/")
@auth.login_required
def index():
    return "hello world"


@app.route('/todo/api/v1.0/get_tasks', methods=['GET'])
def get_tasks():
    print("hello")
    return jsonify({'tasks': tasks})


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['GET'])
def get_tasks_by_id(task_id):
    print("task_id " + str(task_id))
    ta = list(filter(lambda t: t['id'] == task_id, tasks))
    if len(ta) == 0:
        abort(404)
    return jsonify({'tasks': ta[0]})


@app.route('/todo/api/v1.0/tasks/create_task', methods=['POST'])
def create_task():
    print("request json " + str(request.json))
    if not request.json or 'title' not in request.json:
        abort(400)
    task = {
        'id': tasks[-1]['id'] + 1,
        'title': request.json['title'],
        'description': request.json.get("description", ""),
        'done': False
    }
    tasks.append(task)
    return jsonify({'task': task}), 201


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['PUT'])
def update_task(task_id):
    task = filter(lambda t: t['id'] == task_id, tasks)
    if len(task) == 0:
        abort(404)
    if not request.json:
        abort(400)
    if 'title' in request.json:
        abort(400)
    if 'description' in request.json:
        abort(400)
    if 'done' in request.json and type(request.json['done']) is not bool:
        abort(400)
    task[0]['title'] = request.json.get('title', task[0]['title'])
    task[0]['description'] = request.json.get('description', task[0]['description'])
    task[0]['done'] = request.json.get('done', task[0]['done'])
    return jsonify({'task': task[0]})


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['DELETE'])
def delete_task(task_id):
    task = filter(lambda t: t['id'] == task_id, tasks)
    if len(task) == 0:
        abort(404)
    tasks.remove(task[0])
    return jsonify({'result': True})


@app.errorhandler(404)
def not_found(error):
    return make_response(jsonify({'tasks': 'Not Found'}), 404)


if __name__ == '__main__':
    app.run(debug=True)

```

### git
git add .
git commit -a -m "comment"
git remote add origin git@gitlab.com:yuchuan512/my_blog.git 关联到git仓库
git push -u origin master　　推送到分支

### 安装java8
首先添加ppa
$ sudo add-apt-repository ppa:webupd8team/java

然后更新系统
$ sudo apt-get update

最后开始安装
$ sudo apt-get install oracle-java8-installer

$ java -version

万网
yuchuan512@163.com













