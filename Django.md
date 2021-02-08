# Django

[Django英文官方文档](https://docs.djangoproject.com/en/3.1/)

[Django中文官方文档](https://docs.djangoproject.com/zh-hans/3.1/)



## 纯手写WEB框架

### HTTP协议

```python
# HTTP协议
网络协议：
HTTP协议        数据传输是明文
HTTPS协议       数据传输是密文
websocket协议   数据传输是密文

四大特性：
  1. 基于请求响应
  2. 基于TCP、IP作用于应用层之上的协议
  3. 无状态
  4. 短/无链接

数据格式：
  请求首行
  请求头
  \r\n\r\n
  请求体

响应状态码：
  1xx
  2xx    200
  3xx
  4xx    403  404
  5xx    500
  
```

### 简单WEB实现

- **socket实现简单请求-响应**

  ```python
  import socket
  
  server = socket.socket()  # TCP 三次握手四次挥手
  server.bind(('127.0.0.1', 8080))  # IP协议 以太网协议 arp协议...
  server.listen(5)  # 池
  
  # 请求相关的所有信息
  """
  b'GET / HTTP/1.1\r\n
  Host: 127.0.0.1:8080\r\n
  Connection: keep-alive\r\n
  Cache-Control: max-age=0\r\n
  Upgrade-Insecure-Requests: 1\r\n
  User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.146 Safari/537.36\r\nAccept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9\r\n
  Sec-Fetch-Site: none\r\n
  Sec-Fetch-Mode: navigate\r\n
  Sec-Fetch-User: ?1\r\n
  Sec-Fetch-Dest: document\r\nA
  ccept-Encoding: gzip, deflate, br\r\n
  Accept-Language: zh-CN,zh-TW;q=0.9,zh;q=0.8,en-US;q=0.7,en;q=0.6\r\n\r\n'
  """
  
  while True:
      conn, client = server.accept()
      data = conn.recv(1024)
      print(data)  
      # 简单加HTTP头之后再返回 浏览器便可以访问该socket服务端
      conn.send(b'HTTP/1.1 200 OK\r\n\r\nhello world')
      conn.close()
      
  ```

- **增加路径判断功能**

  ```python
  # 需求：1. 在上面基础上 如何做到后缀的不同返回不同的内容
  # 解决：拿到用户输入的 路径 做判断
  
  while True:
      conn, client = server.accept()
      data = conn.recv(1024)
      # print(data)                 # bytes
      data = data.decode('utf-8')  # 得到字符串
      # 获取字符串中特定内容 => 正则 or 字符串有规律可以使用：切割
      conn.send(b'HTTP/1.1 200 OK\r\n\r\n')
      current_path = data.split()[1]  # 获取路径信息
      if current_path == '/index':
          conn.send(b'index.html')
      elif current_path == '/login':
          conn.send(b'login.html')
      else:
          conn.send(b'hello web')
      conn.close()
  
  # 需求 2. 返回html页面
  # 解决：修改返回数据代码 直接rb模式读取html文件并返回
  if current_path == '/index':
      with open(r'temp/index.html', 'rb') as f:
          conn.send(f.read())
  elif current_path == '/login':
      with open(r'temp/login.html', 'rb') as f:
          conn.send(f.read())
  else:
      with open(r'temp/index.html', 'rb') as f:
          conn.send(f.read())
  
  # 上面代码不足之处：
    1. 代码重复 重复造轮子  -- 服务端所有人都要重复写
    2. 手动处理http格式的请求数据 并且只能拿到url后缀 需要拿其他数据怎么办?  -- 数据格式一样 处理的代码其实也大致一样 重复写
    3. 路径处理需要一直使用if..else字据 维护性差
    4. 并发的问题 
  ```


### 借助wsgiref参考实现

**借助wsgiref **

- 没有写服务端代码 
- 没有写请求头处理代码 
- 可以专注业务逻辑的实现

```python
from wsgiref.simple_server import make_server

def run(env, response):
    """
    :param env: 请求相关的所有数据 是一个大字典 wsgiref模块帮你处理好http格式的数据 封装成字典让你更加方便的操作
    :param response: 响应相关的所有数据
    :return: 返回给浏览器的数据
    """
    # print(env)
    # PATH_INFO: 请求路径信息
    current_path = env.get('PATH_INFO')
    response('200 OK', [])    # 响应首行 响应头
    if current_path == '/index':
        return [b'index']
    elif current_path == '/login':
        return [b'login']
    return [b'404 Page not found']

if __name__ == '__main__':
    """
    会实时监听127.0.0.1:8080地址 只有有客户端请求过来
    都会交给run函数处理(触发run函数的运行: run(environ, start_response)
    并注入2个参数：environ, start_response)
    函数： run()
    类：   类加() -> 实例: 能直接调用 -> 实现：__call__ 魔术方法
    
    e.g. flask启动源码：
         make_server('127.0.0.1', 8080, obj)
         __call__
    """
    server = make_server('127.0.0.1', 8080, run)
    server.serve_forever()  # 启动服务端
```

- **对url处理进行封装 优化if..else语句**
```python
from wsgiref.simple_server import make_server

def index(env):
    return 'index'

def login(env):
    return 'login'

def error(env):
    return '404 page not found'

# url与函数的对应关系
# 为什么是函数：组织处理该url访问的业务逻辑代码 不再是简单的一句return
urls = [
    ('/index', index),
    ('/login', login),
]

def run(env, response):
    """
    :param env: 请求相关的所有数据 是一个大字典 wsgiref模块帮你处理好http格式的数据 封装成字典让你更加方便的操作
    :param response: 响应相关的所有数据
    :return: 返回给浏览器的数据
    """
    # print(env)
    # PATH_INFO: 请求路径信息
    current_path = env.get('PATH_INFO')
    response('200 OK', [])    # 响应首行 响应头
    # 定义一个变量 存储匹配到的函数名
    func = None
    for url in urls:
        if current_path == url[0]:
            # 将url对应的函数名赋值给func
            func = url[1]
            break  # 匹配到url 立即结束当前循环
    # 判断func是否有值
    if func:
        res = func(env)
    else:
        res = error(env)
    return [res.encode('utf-8')]

if __name__ == '__main__':
    """
    会实时监听127.0.0.1:8080地址 只有有客户端请求过来
    都会交给run函数处理(触发run函数的运行: run(environ, start_response)
    并注入2个参数：environ, start_response)
    函数： run()
    类：   类加() -> 实例: 能直接调用 -> 实现：__call__ 魔术方法
    
    e.g. flask启动源码：
         make_server('127.0.0.1', 8080, obj)
         __call__
    """
    server = make_server('127.0.0.1', 8080, run)
    server.serve_forever()  # 启动服务端

```

### 模块化

**全部文件放在同一个目录 后期会混乱 难以维护**

```python
# 解决：模块化
Projects/
    main.py      # 上面 wsgiref Server的封装
    urls.py      # 专门处理urls与业务函数影关系的模块
    views.py     # 专门编写业务函数处理逻辑的模块
    templates/   # html模板目录
        index.html
        login.html
        404.html
        ...
# main.py
from wsgiref.simple_server import make_server
from urls import urls
from views import *

def run(env, response):
    """
    :param env: 请求相关的所有数据 是一个大字典 wsgiref模块帮你处理好http格式的数据 封装成字典让你更加方便的操作
    :param response: 响应相关的所有数据
    :return: 返回给浏览器的数据
    """
    current_path = env.get('PATH_INFO')
    response('200 OK', [])    # 响应首行 响应头
    func = None
    for url in urls:
        if current_path == url[0]:
            func = url[1]
            break  # 匹配到url 立即结束当前循环
    if func:
        res = func(env)
    else:
        res = error(env)
    return [res.encode('utf-8')]

if __name__ == '__main__':
    server = make_server('127.0.0.1', 8080, run)
    server.serve_forever()  # 启动服务端
        
# urls.py
from views import index, login, xxx

urls = [
    ('/index', index),
    ('/login', login),
    ('/xxx', xxx),
]

# views.py
def index(env):
    return 'index'

def login(env):
    return 'login'

def error(env):
    return '404 page not found'

def xxx(enx):
    with open('templates/xxx.html', encoding='utf-8') as f:
        return f.read()

```

### 总结

```python
# 按照功能不同拆分之后 后续添加功能只需要再urls.py书写对应关系 然后在views.py书写业务逻辑即可

urls.py        # 路由与视图函数对应关系
views.py       # 视图函数(后端业务逻辑)
templates文件夹 # 专门用来存储html文件 
```

### 动静态网页

```python
"""
静态网页：
  页面上的数据是直接写死的 万年不变
  
动态网页：
  数据是实时获取的
  e.g.:
    1. 后端获取当前时间 展示到html网页上
    2. 数据是从数据库中获取的 展示到html页面上

"""
```



  





## Install

```bash
# 安装django == 指定安装版本
pip install django==x.x.x  
```

## 本质

```bash
# Django本质
Django本身不是Server(不是 nginx or httpd) 只是个WSGI App 就是一个大函数

# 请求过程
浏览器发起一个HTTP Request报文 到TCPServer(HTTPServer)
Server默认监听80 只不过这个Server正好它能提供http请求报文的解析和Http响应报文的封装 就是说Server是HTTP_Server
得有一个调用者 调用Django得这个函数 调用者就是WSGI_Server

# Nginx 把http请求报文 
==> uwsgi软件/gunicorn软件(软件是WSGI Server) 
==> 解析报文并调用Django App大函数 
==> 函数返回值

# request
--> uwsgi 
--> wsgi.py 
--> application 注入两个参数调用 
--> application(environ, start_response)
--> 返回正文给wsgi封装为response报文
--> 前端显示

# 参考 Django中的实现
uwsgi等软件 调用的就是django/flask的wsgi.py 得到 application 
```

- 入口

```python
# 入口
根据wsgi的原理 我们知道Django就是APP 入口是项目的wsgi.py文件
1. 请求request来了后 
2. 交给application(WSGIHandler实例) 
3. application(environ, start_response)调用 
4. 调用start_response后返回get_response返回的响应的结果
```

- Django中的实现 


```python
# wsgi.py
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'cmdb.settings')

# application 就是那个app大函数
application = get_wsgi_application()
```

## ORM快速测试

### 测试脚本

**根据上面原理得出下面这个脚本**

```python
# django 项目统计目录创建 xx.py

import os
import django

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'cmdb.settings')
# 加载django配置
# 在settings.py 配置好数据库连接即可
django.setup(set_prefix=False)

# 我就是那个app大函数
# 返回一个handler类的实例处理每一个请求
# application = get_wsgi_application()
```

### 配置settings.py

**详细配置参考官网**

```python
# MySQL数据库配置
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'name',  # 连接的数据库名称
        'USER': 'user',  # 连接用户
        'PASSWORD': 'password',  # 连接密码
        'HOST': 'host',  # 主机IP
        'PORT': '3306',  # 端口
    }
}

# Django ORM难学 通过日志看对应SQL
# 具体logging模块详细信息 参考Python标准库文档
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': 'DEBUG',
            'propagate': False,
        },
    },
}
```

### 依赖

```bash
pip install mysqlclient
```

### ORM

**ORM 对象关系映射 对象和关系之间的映射 使用面向对象的方式来操作数据库**

```python
# 关系模型和Python对象之间的映射
table  =>  class		# 表 映射为 类
row    =>  object		# 行 映射为 实例
column =>  property     # 字段 映射为 属性(类属性)
```

**实例**

```python
# 描述
表名：student
字段-类型：
 - id-int  
 - name-varchar 
 - age-int
    
# 到Python映射
class Student:
    id = ?某字段类型
    name = ?某字段类型
    age = ?某字段类型
   
# 最终得到的实例
class Student:
    def __init__(self):
        self.id = ?
        self.name = ?
        self.age = ?
```

### Model元编程

**Django Model 背后的故事 -- 元编程**

#### 测试环境

```bash
cd ../project_dir

# 安装django
pip install django

# 创建项目
django-admin startproject cmdb

# 配置修改参考上文

# 创建user包
python manage.py startapp user

# 注册 
一定要在settings.py INSTALLED_APPS 注册user 否则不能迁移
```

#### Model数据库模型

```python
# user/models.py

from django.db import models

# Create your models here.
class User(models.Model):  # 继承的目录 代码复用
    class Meta:  # 元数据
        db_table = 'user'  # 指定mysql中的表名 否则默认为module_class.__name__
    # 字段定义
    id = models.AutoField(primary_key=True)  # 自增 同时是主键 Django只支持单一主键
    # 元编程会在没有主键的请况下 建立一个字段名为:id 的自增主键
    username = models.CharField(max_length=20, null=False)
    password = models.CharField(max_length=128, null=False)  # passwd 密文 hash

    def __repr__(self):
        return "<User {}， {}>".format(self.id, self.username)  # self.id == self.pk 主键

    __str__ = __repr__

# 迁移：Model类生成数据库中的表和字段
```

#### 元类

- **类定义**

```python
class A:  # 语法定义
    pass


# type函数问什么 问你是谁的实例

# type说明什么？ A是type的实例 A称为类对象 由type构造出来的实例
print(1, type(A))
# class构造出来的是类 type是构造类的类
# 元类：  用来构造类的类型
# 元编程: 用编程的方法来编程
# 元数据: 用来描述数据的数据
print(type(type))  # type
# 元类 用来构造类对象 不是用来继承的
# class Model(metaclass=ModelBase) 使用metaclass参数改变元类

# __new__ 两个作用
# 1. 用来实例化
# 2. 用在元编程中

print('=' * 30)
a = A()  # a是A类的实例
print(2, type(a))  # A, a是由A构造出来的实例
```

- **django model 仿写**

```python
class Field:
    def __init__(self, primary_key=False, name=None, null=False):
        self.pk = primary_key
        self.name = name  # ？
        self.null = null

    def __str__(self):
        return "<Field {}>".format(self.name)

    __repr__ = __str__

class Manager:
    pass

# type是Python提供的原始元类
# 不知道怎么构建元类 继承自type解决(因为type是元类)
# 对比：普通类最终继承自object
class ModelBase(type):
    # 元类编程中：构建类对象的
    def __new__(cls, name, bases, attrs):
        print('=' * 30)
        print(cls)
        # name, bases, attrs = args
        # 三元组 (name: 构造类的name, bases:构造出来类的 继承类, attrs:构造出来类的类属性)
        print(name, bases, attrs)
        # attrs['id'] = 'abc'
        print(attrs)
        pks = []  # 主键们
        for k, v in attrs.items():
            # k, v => 'username', Field对象
            if isinstance(v, Field):
                if v.pk:
                    pks.append(v)
                if not v.name:
                    v.name = k
        attrs['pks'] = pks

        # 判断主键不存在 贼默认创建id主键
        if not pks:
            id = Field(primary_key=True, name='id', null=False)
            pks.append(id)
        attrs['pks'] = pks
        print('=' * 30)
        # return None  # 应该返回类对象 而不是None or 其他
        # return super().__new__(cls, name, bases, attrs)
        new_class = super().__new__(cls, name, bases, attrs)
        # 省略1000行
        # 判断objects 挂objects对象
        setattr(new_class, 'objects', Manager())
        return new_class

# 改模子 由type改为使用ModelBase为元类
# 用ModelBase的__new__方法构造Model类
# 本来找type的 指定metaclass改变
# class Model(list, metaclass=ModelBase):
class Model(metaclass=ModelBase):
    pass

# m = Model()  # None() => TypeError
# print(*Model.__dict__.items(), sep='\n')

# User继承自Model Model类由ModelBase构造 那么User也是由ModelBase构造
# 不用显示写：metaclass=ModelBase
class User(Model):  # 由元类构造
    username = Field(null=False)
    password = Field(null=False)

print(User.pks)

# python manage.py runserver  # 测试用的wsgi server
```



## Useage

```bash
# 使用脚手架工具 创建项目相关文件
django-admin startproject name [dir]  

django-admin startproject cmdb .

python manage.py startapp user  
# 创建一个业务包 项目中的一个应用 一个功能模块
# 创建包之后 一定要在 项目settings.py文件中 注册一下：配置 INSTALLED_APPS = [..., 'user',]   非常重要
# 创建包(模块)之后 即可在里面进行CODE
```



## Django目录结构

```python
# 目录结构
WEBTest  # 项目目录
  -- first  # 主目录 主要是项目配置、入口
    -- __init__.py  # 包文件
    -- asgi.py      # 2.x 不支持asgi 各种gi的入口文件
    -- settings.py  # 全局配置文件 重要
    -- urls.py      # 路径映射
    -- wsgi.py      # 入口
  
  -- user          # 和fisrt平行 功能模块
    -- migrations  # 数据库迁移相关的目录
      -- __init__.py
    -- __init__.py  # 包文件
    -- admin.py     # 后台管理
    -- apps.py      # 跟该应用相关的数据
    -- models.py    # ORM构建模型类
    -- tests.py     # 测试脚本
    -- views.py     # 视图
    
  -- manage.py      # 相当于 django-admin 代理
```

## DjangoRestFramework

[Django REST Framework文档](https://www.django-rest-framework.org/)

