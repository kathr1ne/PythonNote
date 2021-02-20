# Django

[Django英文官方文档](https://docs.djangoproject.com/en/3.1/)

[Django中文官方文档](https://docs.djangoproject.com/zh-hans/3.1/)

## Django请求生命周期

### 流程

```python
"""
浏览器发送请求 => WEB网关服务接口 => Django框架
                                 -> 中间件 middleware (-> 缓存数据库 拿到则返回)
                                 -> 路由层 urls.py
                                 -> 视图层 views.py
                                    -> 模板层 templates
                                    -> 模型层 modes.py
                                 -> 拿到数据/模板 渲染后按路径返回
                            
请求经过WEB网关服务接口 需要了解：
  - django自带的是wsgiref
    1. 请求来的时候 解析请求 并封装为request对象
    2. 响应走的时候 打包处理
  - django自带的wsgiref模块本身能够支持的并发量很小 最多1000左右
  - 上线会换成 nginx(反向代理) + uwsgi
  
  WSGI/wsgiref/uwsgi是什么关系?
    - WSGI 是协议
    - wsgiref/uwsgi是实现该协议的功能模块
    
请求经过Django框架 需要了解：
  -> 先经过Django中间件(类似与django的保安 门户)
  -> 路由层 urls.py 路由匹配与分发 识别路由匹配对应的视图函数
  -> 视图层 views.py 网站整体的业务逻辑
     - 视图层可能需要模板 -> 模板层(templates文件夹) 网站所有的html页面
     - 视图层可能需要数据 -> 模型层 models.py **重要** ORM
     - 视图层拿到数据之后 渲染并按路径返回
"""
```

### 扩展

**缓存数据库**

```python
"""
提前已经将你想要的数据准备好 你来直接拿即可
 - 减轻后端压力
 - 提高效率和响应时间
    
缓存数据库在请求流程中的位置   
  - 经过中间件之后 直接请求缓存数据库
    - 拿到 直接返回
    - 没拿到 走正常流程 然后存一份到缓存数据库并返回
    
当你在修改你的数据的时候 你会发现数据并不是立即修改完的
而是需要经过一段时间才会修改(还在访问的缓存的内容)
"""
```

## 注意事项

```python
# 如何让你的计算机能够正常的启动Django项目

"""
  1. 计算机的名称不能有中文
  2. 一个Pycharm窗口只开一个项目
  3. 项目里面所有的文件也尽量不要出现中文
  4. Python解释器结论使用 3.4-3.6之间的版本
    4.1. 如果你的项目报错 点击最后一个报错信息 去源码中把逗号删掉
"""

# Django版本问题
推荐使用：LTS 官方长期支持维护的版本
"""
1.x 2.x 3.x(成熟版本出来后考虑)
1.x 和 2.x 本身差距并不大
"""  
```

## 安装

```bash
# 安装django == 指定安装版本
$ pip install django==x.x.x

# e.g. 直接安装 会自动卸载旧的安装新的
$ pip install django==1.11.11

# 验证是否安装成功
$ django-admin  # 看是否输出帮助文档
```

## django基本操作

- **命令行操作**

```python
# 1. 创建django项目
# 如果已经实现创建项目目录 [目录] 直接换为 .(当前目录)
$ django-admin startproject [项目名] [目录]
$ django-admin startproject [name] .

# e.g.
$ cd MinhoDjango  # 进入已存在的目录 作为项目根目录
$ django-admin startprojcet mysite .

# 2. 启动django目录
$ python manage.py runserver  # 默认: 127.0.0.1:8080


# 3. 创建应用
"""
Next, start your first app by running python manage.py startapp [app_label].

应用名应该做到见名知意：
  - user
  - order
  - web
  - ...
"""
$ python manage.py startapp app01
```

- **Pycharm创建**

```python
"""
1. File --> New Project --> Django项目

2. 启动
    2.1 还是用命令行启动
    2.2 点击绿色小箭头即可
   
3. 创建应用app
    3.1 还是用命令行创建
    3.2 Tools --> Run manage.py Task... # 进入shell
        startapp app01  # 创建app01 应用

4. 修改端口号以及创建server
   右上角 --> Edit Configure

"""
```

- **区别**

**命令行与pycharm创建的区别**

```python
"""
  1. 命令行创建不会自动有 root_projects/templates 文件夹 需要你自己手动创建 而pycharm创建会自动帮你创建并且还会自动在配置文件中配置对应的路径
  意味着 你在用命令创建django项目的时候 不单单需要创建templates文件夹 还需要去配置文件中配置路径
"""

# 命令行创建
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
    },
]

# Pycharm创建
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates']
    },       
]
```

## 应用

```python
"""
django 是一款专门用来开发app的web框架

django框架就类似一所大学(空壳子 需要自己创建具有不同功能的app)
app就类似大虚额里面的各个学院(学院--具体功能的app)

比如开发淘宝：
  - 订单相关
  - 用户相关
  - 投诉相关
  - 创建不同的app对应不同的功能
  
学课系统：
  - 学生功能
  - 老师功能
  
一个app就是一个独立的功能模块
"""
```

**创建的应用 一定要去配置文件中注册**

```python
# Application definition
"""
一定要注册 否则应用不生效
创建出来的应用 第一步先去配置文件中注册 其他的先不要给我改
备注: 在使用Pycharm创建项目的时候 Pycharm可以帮你创建一个app并自动注册(仅一个)
"""

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01.apps.App01Config',  # 全写
    'app01',  # 简写
    'app02',
]  # 全写 or 简写 选择其中一种即可

```

## 主要文件介绍

```python
"""
MinhoDjango/
    - manage.py          django的入口文件
    - db.sqlite3         django自带的sqlite3数据库(小型数据库 功能不全还有BUG)
    - mysite/
        - __init__.py
        - settings.py    配置文件
        - urls.py        路由与视图对应关系(路由层)
        - wsgi.py        wsgiref模块(忽略)
    - app01
        - migrations/    所有的数据库迁移记录(类日志记录)
            - __init__.py
        - __init__.py
        - admin.py       django后台管理
        - apps.py        注册使用
        - models.py      数据库相关的 模型类(模型层 ORM)
        - tests.py       测试文件(忽略)
        - views.py       视图函数(视图层)
"""
```

## 配置文件

```python
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True  # 上线之后改为Flase

ALLOWED_HOSTS = []  # 上线之后可以写 '*'  允许所有

# 注册的app(app就是自带的功能模块) django一创建出来 自带6个功能模块
# Application definition
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01.apps.App01Config',
    'app02',
]

# Django中间件
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# HTML存放路径配置
TEMPLATES = []

# 项目指定数据库配置 默认sqlite3
# Database
# https://docs.djangoproject.com/en/3.1/ref/settings/#databases
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Password validation
# https://docs.djangoproject.com/en/3.1/ref/settings/#auth-password-validators
AUTH_PASSWORD_VALIDATORS = []
```

## Django小白必会三板斧

### HttpResponse

**HttpResponse 直接返回字符串**

```python
from django.shortcuts import HttpResponse

# Create your views here.

# 视图函数必须要接收一个形参：request
def index(request):
    """
    :param request: 请求相关的所有数据对象 比之前的environ更好用 是对象 直接. 取属性 比字典好用
    :return:
    """
    data = HttpResponse('Hello, Django')
    print('1', data)
    print('2', type(data))
    return HttpResponse("Hello, Django")

>>> 1 <HttpResponse status_code=200, "text/html; charset=utf-8">
>>> 2 <class 'django.http.response.HttpResponse'>
```

### render

**render 返回html文件 使用渲染之后的模板**

```python
from django.shortcuts import render

# Create your views here.
def index(request):
    """
    :param request: 请求相关的所有数据对象 比之前的environ更好用 是对象 直接. 取属性 比字典好用
    :return:
    """
    return render(request, 'myfirst.html')

# 命令行创建 需要配置模板目录
$ mkdir ${root_projects}/templates
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
	},
]
```

**render两种传值方式**

```python
def index(request):
    """
    1. 直接传一个字典 模板使用key进行使用 精确传值 使用哪个传哪个
    
    2. local()
    locals() 会将所在的名称空间中所有的名字全部传递给HTML页面
    当需要传的数值特别多的时候 使用 locals() 传值
    """
    user_dict = {'username': 'Minho', 'age': 25}
    # render 传值 1.
    # return render(request, 'myfirst.html', {'data': user_dict, 'date': '2021-02-09'})
    
    # render 传值 2.
    return render(request, 'myfirst.html', locals())
```

### redirect

**重定向**

```python
from django.shortcuts import redirect

# Create your views here.
def index(request):
    """
    :param request: 请求相关的所有数据对象 比之前的environ更好用 是对象 直接. 取属性 比字典好用
    :return:
    """
    # return redirect('https://www.baidu.com/')  # 重定向到外部网址
    return redirect('/home/')  # 302重定向到 自己的网站 /home/

def home(request):
    return HttpResponse('home')
```

## 登录功能实现

```python
# 登录功能
"""
html文件：默认都放在templates文件夹下
静态文件：将网站使用的静态文件默认都放在static文件夹下

静态文件：前端已经写好 能够直接调用使用的文件
  - 网站写好的js文件
  - 网站写好的css文件
  - 网站用到的图片文件
  - 第三方前端框架
  ...
  总结：拿来就可以直接使用的  
"""

# django默认不会自动创建static文件夹 需要你手动创建
一般情况下 我们会在static文件夹内 还会做进一步的划分处理
=> 解耦合 方便管理
  - static
    - js
    - css
    - img
    - 其他第三方文件
   
"""
在浏览器中 输入url能够看到对应的资源
是因为后端提前开设了该资源的接口 -- 路由分发
如果访问不到资源 说明后端没有开设该资源的接口
"""

# 访问 login页面 没有加载bootstrap样式的问题
访问 http://127.0.0.1:8000/static/bootstrap-3.3.7-dist/css/bootstrap.min.css 报错 说明后端没有开设该资源的请求路由出来

# 静态文件配置 -- 重点
按照下面方法在settings.py配置文件配置static相关静态文件配置之后 即可访问引用的本地static文件夹下面的静态资源 -- 类似于django自己帮我们开设了对应的路由接口
```

**login.html示例**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Login In</title>
    <link rel="stylesheet" href="/static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
    <script src="/static/bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
    <script src="/static/js/jquery-3.5.1.min.js"></script>

</head>
<body>
<h1 class="text-center">登录</h1>
</body>
</html>
```

###  **静态文件配置**

```python
# 类似于访问静态文件的令牌
# 如果你想要访问静态文件 你就必须以static开头
# 切记：该配置 是配置访问url的开头的值 令牌 并不是静态文件存放的目录
STATIC_URL = '/static/'  

"""
/static/  -  令牌
去下面STATICFILES_DIRS列表里面的目录里面 按顺序查找引用的文件
  eg. bootstrap-3.3.7-dist/css/bootstrap.min.css
  都没有找到 则报错
"""

# 静态文件配置 列表 可以有多个(令牌持有者可以访问的文件路径)
# 静态文件真正存放的目录 可以有多个目录 按顺序解析
# 前面的找到请求的文件之后 不再向后查找
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
    os.path.join(BASE_DIR, 'static1'),
]


# 浏览器 设置页面不缓存的方法

"""
当你在写django项目的时候 可能会出现后端代码修改了但是前端页面没有变化的情况
  1. 在同一个端口开了好几个django项目 一直在跑的是第一个django项目
  2. 浏览器缓存的问题 按照下面方法解决
  右键 --> 检查 --> 设置图标(settings) --> Preferences --> Network --> 勾选上 Disable cache(while Devtools is open)
"""
```

**静态文件动态解析**

```html
<!-- 静态文件动态解析
使用模板语法 会自动解析setting.py里面配置的 STATIC_URL 然后自动拼接url
e.g: 
STATIC_URL = '/static/'
url -> /static/bootstrap-3.3.7-dist/js/bootstrap.min.js

or

STATIC_URL= '/xxx/'
url -> /xxx/bootstrap-3.3.7-dist/js/bootstrap.min.js
-->

{% load static %}
<link rel="stylesheet" href="{% static 'bootstrap-3.3.7-dist/css/bootstrap.min.css' %}">
<script src="{% static 'bootstrap-3.3.7-dist/js/bootstrap.min.js' %}"></script>
```

**form表单**

```python
# form表单 默认是GET方法请求数据
# ?后面的内容为参数 不参与(不影响)路径匹配
http://127.0.0.1:8000/login/?username=minho&password=123131313
   
"""
form表单action参数
  1. 不写 默认向当前所在的url提交数据
  2. 全写 指名道姓
  3. 只写后缀 /login/
"""

"""
form表单提交方法 改为post提交数据 提交会报错
  Forbidden (403)
  CSRF verification failed. Request aborted.

解决：
  在前期 我们使用django提交post请求的时候 需要去配置文件中注释掉一行代码
  注释掉 中间件配置中的 CsrfViewMiddleware
"""

# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# views.login处理函数
def login(request):
    # 返回一个登录界面
    """
    GET请求和POST请求 应该有不同的处理机制 -> 如何处理?
    引入下面 request对象方法初始 探索django封装的request对象
    :param request:
    :return:
    """
    print('enter login...')
    return render(request, 'login.html')
```

### request对象方法初识

- **request.method**

```python
def login(request):
    # 返回一个登录界面
    """
    GET请求和POST请求 应该有不同的处理机制 -> 如何处理?
    :param request: 请求相关的数据对象 里面有很多简易的方法
    :return:
    """
    # 返回请求方式 并且是全大写的字符串 <class 'str'>
    # print(request.method)  
    
    """
    这种方式 2层逻辑 推荐下一种
    if request.method == 'GET':
        print('Method GET')
        return render(request, 'login.html')
    elif request.method == 'POST':
        return HttpResponse("收到了 POST请求")
    """
    if request.method == 'POST':
        return HttpResponse('收到POST')
    return render(request, 'login.html')
```

- **request.POST**

```python
# 获取用户post请求提交的普通数据 不包含文件
request.POST
  - request.PSOT.get()      # 只获取列表最后一个元素
  - request.POST.getlist()  # 直接将整个列表取出
```

- **request.GET**

```python
# 获取用户get请求提交的数据
# get请求携带的数据是有大小限制的 大概只有4KB左右
# 而post请求则没有限制
request.GET
  # 获取到的数据类型 方法的使用 和 request.POST 一模一样
  - request.GET.get()      # 只获取列表最后一个元素
  - request.GET.getlist()  # 直接将整个列表取出
    
def login(request):
    # 返回一个登录界面
    if request.method == 'POST':
        username = request.POST.get('username')
        return HttpResponse('收到POST')
    # 注意get 和 getlist方法区别
    hobby = request.GET.getlist('hobby')
    return render(request, 'login.html')
```

------



# Django ORM

## Django本质

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
        'CHARSET': 'utf8'
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

**mysqlclient or pymysql**

```python
# 如果要使用pymysql 在项目包文件__init__.py中加入下面代码
import pymysql
pymysql.install_as_MySQLdb()

mysqlcilent  # c语言编写 速度快 推荐
pymsyql      # python原生 兼容度高
```

**设置sql_mode**

```python
# 设置sql_mode
# https://docs.djangoproject.com/zh-hans/3.1/ref/databases/

'OPTIONS': {
    "init_command": "SET sql_mode='STRICT_TRANS_TABLES'",
}
```

### 依赖

```bash
pip install mysqlclient
```

## ORM

### 简介

**ORM 对象关系映射 对象和关系之间的映射 使用面向对象的方式来操作数据库**

```python
# 关系模型和Python对象之间的映射
table  =>  class		# 表 映射为 类
row    =>  object		# 行 映射为 实例
column =>  property     # 字段 映射为 属性(类属性)

# 不足
封装程度太高 有时候sql语句的效率偏低 需要你自己写SQL语句
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

### 创建model类

```python
# 去应用下面的 models.py 文件
from django.db import models
# Create your models here.
# model类 编写
class User(models.Model):
    id = models.AutoField(primary_key=True)  # id int primary_key auto_increment
    username = models.CharField(max_length=32)  # username varchar(32)
    password = models.IntegerField()  # password int

class Author(models.Model):
    """
    django orm当你不定义主键字段的时候 orm会自动帮你创建一个名为id的主键字段
    后续我们在创建模型表的时候 如果主键字段名没有额外的叫法 那么主键字段可以省略不写 orm帮我们补
    """
    username = models.CharField(max_length=32)
    password = models.IntegerField()
```

### 数据库迁移

```bash
# 数据库迁移命令
# 只要你修改了models.py中跟 **数据库** 相关的代码 就必须重新执行下面两条命令
$ python manage.py makemigrations    # 将操作记录基础出来(migrations文件夹)
$ python manage.py migrate  # 将操作真正的同步到数据库中
```

### 字段类型

https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/

```python
CharField     # 必须要指定 max_length参数 不指定会直接报错
verbose_name  # 该参数是所有字段都有的 用来对字段的解释
...
```

## 字段的增删改查

### **字段的增加**

```python
# 给表增加字段的时候 表里面已经有数据的情况
1. 可以在终端内直接给出默认值 django命令行会在终端给出选择
    $ You are trying to add a non-nullable field 'age' to user without a default; we can't do that (the database needs something to populate exi
    sting rows).
    $ Please select a fix:
     1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
     2) Quit, and let me add a default in models.py

2. 设置该字段可以为空(根据实际情况考虑)
    info = models.CharField(max_length=32, verbose_name='个人简介', null=True)
    
3. 直接给字段设置默认值
    hobby = models.CharField(max_length=32, verbose_name='爱好', null=False, default='study')
```

### **字段的修改**

```bash
# models.py 修改后 直接执行迁移命令即可：
$ python manage.py makemigrations
$ python manage.py migrate
```

### **字段的删除**

```python
# 直接注释对应的字段 然后直接执行迁移命令即可
$ python manage.py makemigrations
$ python manage.py migrate

# 比较严重的问题
迁移命令执行完毕之后 数据库对应的数据也会被删除

# **注意**
"""
在操作models.py的时候 一定要细心
  - 千万不要随意注释一些字段
  - 执行迁移命令之前 最好先检查下自己写的代码
"""

# 个人建议： 当你离开你的计算机之后 一定要锁屏
```

## 数据的增删改查

[`QuerySet` API 参考](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/)

### 查

[filter](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#filter)

```python
# 查
models.User.objects.filter(username=username)

"""
返回值可以先看成是列表套数据对象的格式
它也支持索引取值 切片操作 但是不支持负索引
它也不推荐你使用索引的方式取值

models.User.objects.filter(username=username)[0]
models.User.objects.filter(username=username).first()
"""

# 简单登录功能实现 使用 ORM 查询
def login(request):
    if request.method == 'POST':
        # 获取用户提交的用户名和密码 利用orm 校验数据
        username = request.POST.get('username')
        password = request.POST.get('password')
        # 去数据库查询数据
        from app01 import models
        user_obj = models.User.objects.filter(username=username).first()
        if user_obj:
            # 比对密码是否一致
            if password == user_obj.password:
                return HttpResponse('Login Success')
            return HttpResponse('Login Failed, Password Error')
        return HttpResponse('用户不存在')
    return render(request, 'login.html')
```

### 增

[create](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#create)

```python
# create 返回当前被创建的对象本身
res = models.User.objects.create(username=username, password=password)

# 实例化对象 调用save方法
user_obj = models.User(username=username, password=password)
user_obj.save()  # 保存数据
```

### 改

[QuerySet.update](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#django.db.models.query.QuerySet.update)

```python
# 先将数据库中的数据全部展示到前端 然后给每一个数据2个按钮 一个编辑一个删除
ef userlist(request):
    # 查询用户表里面 所有的数据
    # 方式1 不推荐
    # data = models.User.objects.filter()

    # 方式2
    user_queryset = models.User.objects.all()
    return render(request, 'userlist.html', {'user_info': user_queryset})

# 编辑功能
  1. 点击编辑按钮朝后端发送编辑数据的请求
  """
  Q: 如何告诉后端 用户想要编辑哪条数据?
  A: 将编辑按钮所在的哪一行数据的 主键值 发送给后端 -- 唯一确定值
  
  Q: 如何发送主键值?
  A: <a> href="/edit_user/?user_id={{ user.id }}" </a>
     利用url问号后面携带参数的方式 传主键值给后端获取
  """

  2. 后端查询出用户想要编辑的数据对象 展示到前端页面供用户查看和编辑
    
# view 代码实现
def userlist(request):
    # 查询用户表里面 所有的数据
    # 方式1 不推荐
    # data = models.User.objects.filter()
    
    # 方式2
    user_queryset = models.User.objects.all()
    return render(request, 'userlist.html', {'user_info': user_queryset})

def edit_user(request):
    # 获取url问号后面携带的参数 href="/edit_user/?user_id={{ user.id }}"
    user_id = request.GET.get('user_id')
    # 查询当前用户想要编辑的数据对象
    user_obj = models.User.objects.filter(id=user_id).first()

    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        # 如果是post提交 去数据库修改对象的数据内容
        # 修改方式1
        """
        将filter查询出来的列表中所有的对象全部更新 批量更新操作
        只会修改被修改的字段
        """
        # models.User.objects.filter(id=user_id).update(username=username, password=password)

        # 修改方式2
        """
        该方式 当字段非常多的时候 效率会特别的低
        从头到尾将数据的所有字段全部更新一遍 无论该字段是否被修改
        """
        user_obj.username = username
        user_obj.password = password
        user_obj.save()  # 内部自动识别 不是保存 而且更新

        # 跳转到数据的展示页面
        return redirect('/userlist/')

    # 将数据对象展示到页面上
    return render(request, 'edit_user.html', {'user_info': user_obj}

# 删除功能

```

### 删

[QeurySet.delete](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#django.db.models.query.QuerySet.delete)

```python
# 删除功能
"""
跟编辑功能逻辑类似
"""

def del_user(request):
    # 获取用户想要删除的数据id值
    user_id = request.GET.get('user_id')
    # 直接去数据库中找到对应的数据 删除即可
    """delete 批量删除"""
    models.User.objects.filter(id=user_id).delete()
    # 跳转到展示页面
    return redirect('/userlist/')


# 数据删除 最好不要真正的删除 添加 is_delete字段
"""
真正的删除功能 需要二次确认
删除数据内部其实并不是真正的删除 我们会被数据添加一个标识字段 用来表示当前数据是否被删除 
如果数据被删除 仅仅是将字段修改一下状态
展示的时候 筛选一下该字段即可

username    password    is_delete
minho        123         0
karubin      123         1

数据值钱 删除危险且浪费 一般情况下 切记不要真正的去删除数据
"""

```

## orm创建表关系

```python
"""
表与表之间的关系
  - 一对多
  
  - 多对多
  
  - 一对一

判断表关系的方法：换位思考
"""
e.g.
图书表 book
id    title    price
1     django   213.12
2     flask    123.33
3     c++      89.08

出版社表 publish
id      name     address
1     上海出版社   上海
2     北京出版社   北京

作者表 author
id    name    age
1     minho   25
2     kimi    37

book2author
id  book_id  author_id
1    1            1
2    1            2
3    2            2
4    3            1

"""
图书和出版社 一对多的关系 外键字段建在多的那一方-book
图书和作者   多对多关系  创建第三张表来专门存储-book2author
一对一 应用场景：
  - 拆表 (作者表 与 作者详情表是一对一)
    - 作者表
    - 作者详情表
"""
```

[一对多ForeignKey](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.ForeignKey)

[多对多ManyToManyField](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#manytomanyfield)

[一对一OneToOneField](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#onetoonefield)

```python
from django.db import models

# Create your models here.
# 创建表关系 先将基表创建出来 然后再添加外键字段
class Book(models.Model):
    title = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=8, decimal_places=2)  # 小数总共8位 小数点后面占2位
    """
    图书和出版社是一对多 并且书是多的一方 所以外键字段放在book表里面
    如果字段对应的是ForeignKey 那么ORM会自动在字段后面加_id -> publish_id
    后面定义ForeignKey的时候 不要自己加_id
    """
    publish = models.ForeignKey(to='Publish')  # 默认就是与出版社表的主键字段做外键关联 可以通过to_field设置
    """
    图书和作者是多对多关系 外键字段建在任意一方均可 但是推荐创建再查询频率较高的一方 (查询方便)
    authors是一个虚拟字段 主要是用来高速ORM 书籍表 和 作者表 是多对多关系 让ORM自动帮你创建第三张表关系
    
    django 1.x 自动级联更新删除 2.x 3.x需要加参数
    """
    authors = models.ManyToManyField(to='Author')

class Publish(models.Model):
    name = models.CharField(max_length=32)
    address = models.CharField(max_length=64)

class Author(models.Model):
    name = models.CharField(max_length=32)
    age = models.IntegerField()
    """
    作者与作者详情是一对一关系 外键字段建在任意一方都可以 但是推荐创建在查询频率较高的表中
    OneToOneField 也会自动给字段加_id后缀
    """
    author_detail = models.OneToOneField(to='AuthorDetail')

class AuthorDetail(models.Model):
    phone = models.BigIntegerField()  # 手机号 或者直接用字符类型
    address = models.CharField(max_length=32)
    
"""
ORM中如何定义三种关系
  一对多：publish = models.ForeignKey(to='Publish')
  多对多：authors = models.ManyToManyField(to='Author')
  一对一：author_detail = models.OneToOneField(to='AuthorDetail')
  
  ForeignKey 和 OneToOneField关系字段 会自动在字段后面加_id后缀
  ManyToManyField关系字段 会自动创建第三张关系对应表 无需我们手动创建
"""

# 注意
"""
1. 在django1.x版本中 外键默认都是级联更新删除的
2. 多对多的表关系可以有好几种(三种)创建方式 这里只是其中一种
3. 针对外键字段里面的其他参数 暂时不做考虑 详情可以参考上面官网链接
"""
```

**on_delete参数补充**

[ForeignKey.on_delete](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.ForeignKey.on_delete)

```python
on_delete=models.CASCADE,     # 删除关联数据,与之关联也删除
on_delete=models.DO_NOTHING,  # 删除关联数据,什么也不做
on_delete=models.PROTECT,     # 删除关联数据,引发错误ProtectedError
on_delete=models.SET_NULL,    # 删除关联数据,与之关联的值设置为null（前提该字段需要设置为可空,一对一同理）
on_delete=models.SET_DEFAULT, # 删除关联数据,与之关联的值设置为默认值（前提FK字段需要设置默认值,一对一同理）
on_delete-models.SET(),       # 删除之后执行一个函数
```



------



## Model元编程

**Django Model 背后的故事 -- 元编程**

### 测试环境

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

### Model数据库模型

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

### 元类

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



# 路由层

## 路由匹配

```python
# 路由匹配
url(r'test', views.test),       
url(r'testadd', views.testadd),
"""
http://127.0.0.1:8000/testadd  永远访问不到 永远访问的是views.test视图函数

url方法的第一个参数是正则表达式
只要第一个参数正则表达式能够匹配到内容 就会立刻停止往下匹配 直接执行对应的视图函数
"""

# 加斜杠/ django匹配的时候会自动加斜杠去匹配
"""
http://127.0.0.1:8000/testadd  第一次匹配 匹配不到 - 301
django会自动在末尾加一个斜杠 进行第二次匹配 - 200 OK

django内部帮你做的重定向
  - 一次匹配不行
  - url末尾加斜杠 再来一次
"""
url(r'test/', views.test),       
url(r'testadd/', views.testadd),
  
# 自动加斜杠功能可以取消 配置settings.py
APPEND_SLASH = False  # 取消自动加斜杠 默认为True 不建议修改

# QA
Q: GET /msafbsvfbtest/ 可以匹配到 test/
A: url(r'^test/', v2.test)   # ^ 解决   
    
Q: GET /test/sdanakvdbkaadad/daskndakjd 可以匹配到 test/
A: url(r'^test/$', v2.test)  # ^test$ 正则精确 test
    
Q：如何写首页URL
A: url(r'^$', v2.home)  # 首页匹配 不能写成url(r'', v2.home)
    
Q: 尾页(404页面 所有页面都没找到) 需要放在最后
A: url(r'', v2.error)  # 了解即可 不这样写
```

## URL分组

```python
"""
分组：就是给某一段正则表达式用小括号括起来
"""
url(r'^test/\d+$', v2.test)  # 正常访问
url(r'^test/(\d+$)', v2.test)  # 分组后 报错TypeError test() takes 1 positional argument but 2 were given
```

### 无名分组

```python
url(r'^test/(\d+$)', v2.test)  
# 分组后 报错TypeError 缺少位置参数
# test() takes 1 positional argument but 2 were given

# 修改视图函数接收分组传递的参数
def test(request, args):
    print(args)
    return HttpResponse('test')

"""
无名分组 就是将括号内正则表达式匹配到的内容当作 位置参数 传递给后面的视图函数
"""
```

### 有名分组

```python
"""
(?P<name>) 可以给正则表达式 分组并起一个别名
有名分组 就是将括号内正则表达式匹配到的内容当作 关键字参数 传递给后面的视图函数
"""
url(r'^testadd/(?P<year>\d+)', v2.testadd) 
# 起别名后 报错 TypeError 缺少关键字参数
# testadd() got an unexpected keyword argument 'year'

# 增加关键字对应的形参
def testadd(request, years):
    print(years)
    return HttpResponse('testadd')
```

### 不能混用

```python
url(r'^index/(\d+)/(?P<year>\d+)/', v2.index)

def index(request, args, year):
    print(args, year)
    return HttpResponse("index")

>>> TypeError at /index/12313/32131313/
>>> index() missing 1 required positional argument: 'args'
    
# 总结： 不能混用 但是 同一个分组可以使用N多次
url(r'^index/(\d+)/(\d+)/(\d+)/', views.index),
url(r'^index/(?P<year>\d+)/(?P<month>\d+)/(?P<day>\d+)/', views.index),

def index(request, *args, **kwargs):
    print(args)    # ('12', '3123', '42341')
    print(kwargs)  # {'year': '12', 'month': '3123', 'day': '42341'}
    return HttpResponse("<h1>index</h1>")
```

### 反向解析

```python
"""
本质：通过一些方法得到一个结果 该结果可以访问到对应的url 从而触发对应视图函数的运行

步骤：
  1. 先给路由与视图函数起一个别名
  url(r'^func_minho/', views.func, name='alias_func'),
  
  2. 反向解析
    2.1 后端反向解析
    from django.shortcuts import reverse, HttpResponseRedirect
    url = reverse('alias_func')
    return HttpResponseRedirect(url)
        
    2.2 前端反向解析 (模板语法)
    <a href="{% url 'alias_func' %}">111</a>
"""
```

- **最简单的请况 url第一个参数里面没用正则符号**

```python
# url
url(r'^home/', views.home, name='home_page'),

"""
给url函数添加一个 name参数(**唯一**) 起一个名字 类似于别名 
注意: **别名唯一 别名不能冲突**
我可以访问到这个名字(url正则可以随意修改) 然后获取到对应的url 从而触发视图函数的运行

1. 前端
  {% url 'alias_name' %}    
  alias_name对应url函数里面的 name参数对应的字符串

2. 后端
   reverse()
"""
```

- **无名分组反向解析**

```python
# url
url(r'^index/(\d+)/', views.index, name='alias_index'),

# 前端
<a href="{% url 'alias_index' 123 %}">111</a

# 后端
reverse('alias_index', args=(1,))  # /index/1/

"""
这个数字写代码的时候应该放什么?
  - 数字 一般情况下放的是数据的主键值 来做数据的编辑和删除
  # urls.py
  url(r'^edit/(\d+)/', views.edit, name='xxx')
  
  # views.py
  def edit(request, edit_id):
      reverse('xxx', args=(edit_id,))
  
  # html
  {% for user in user_queryset %}
  <a href='{% url 'xxx' user.id %}'>编辑</a>
  {% endfor %}
  
"""
```

- **有名分组反向解析**

```python
# url
url(r'^func/(?P<year>\d+)', views.func, name='alias_func')

# 前端
<a href="{% url 'alias_func' year=2009 %}">111</a>
<a href="{% url 'alias_func' 2021 %}">222</a>

# 后端
1. 有名分组写法一
reverse('alias_func', kwargs={'year': 2010})

2. 有名分组 简便写法
reverse('alias_func', args=(2021,))
```

## 路由分发

```python
"""
特点：django 每一个应用 都可以有自己的templates文件夹 urls.py static文件夹
  正是基于上述特点 django能够非常好的做到分组开发(每个人只写自己的app)

  多人开发完之后 只需要将每个人书写的app全部拷贝到一个新的django项目中 然后在配置文件里面注册所有的app 再利用路由分发的特点将所有的app整合起来
  
  当一个django项目中的url特别多的时候 总路由urls.py代码非常冗余 不好维护
  解决: 利用路由分发来减轻总路由的压力
"""

# 路由分发
"""
利用路由分发之后 总路由不在处理 路由与视图函数的直接对应关系 而是做一个分发处理
处理：识别当前url是属于哪个应用下的 然后直接分发给对应的应用处理
"""
```

**路由分发实现**

```python
from django.conf.urls import url, include
from app01 import urls as app01_urls
from app02 import urls as app02_urls

# 总路由
"""
只要url是app01开头就会自动将url中app01后面的路径交给 app01下的urls.py去做匹配
"""
urlpatterns = [
    # 路由分发
    # 只要url前缀是app01开头的 全部交给app01处理
    url(r'^app01/', include(app01_urls)), 
    # 只要url前缀是app02开头的 全部交给app02处理
    url(r'^app02/', include(app02_urls)),  
]

# 子路由 app01.urls
from django.conf.urls import url
from app01 import views
urlpatterns = [
    url(r'^reg/', views.reg)
]

# 子路由 app02.urls
from django.conf.urls import url
from app02 import views
urlpatterns = [
    url(r'^reg/', views.reg)
]


# 总路由 推荐写法 简便
url(r'^app01/', include('app01.urls')),
url(r'^app02/', include('app02.urls')),
# 注意事项：总路由的url 千万不能加$结尾 加了之后 无法继续向下匹配
```

## 名称空间

**解决url别名 命名冲突的问题 了解即可 可以不用 保证别名不同即可**

```python
# 当多个应用出现了相同的别名 反向解析是否会自动识别对应的前缀

"""
正常请况下的反向解析 是没有办法自动识别前缀的
"""

# 解决：利用名称空间

# 总路由加名称空间：namespace
urlpatterns = [
    url(r'^app01/', include('app01.urls', namespace='app01')),
    url(r'^app02/', include('app02.urls', namespace='app02')),
]

# 子路由 别名一致
"""
# app01.urls
urlpatterns = [url(r'^reg/', views.reg, name='reg')]

# app02.urls
urlpatterns = [url(r'^reg/', views.reg, name='reg')]
"""

# 解析的时候
  - 前端
    <a href="{% url 'app01:reg' %}">111</a>
    <a href="{% url 'app02:reg' %}">222</a>
    
  - 后端
    reverse('app01:reg')
    reverse('app02:reg')
```

**总结：其实只要保证别名不冲突 就没有必要使用名称空间**

```python
"""
一般情况下 有多个app的时候 我们在起别名的时候 会加上对应app的前缀
这样的话 就能够确保多个app之前名字不冲突的问题
"""
```

## 伪静态

**了解即可**

```python
"""
静态网页
  数据是写死的 万年不变
  
伪静态
  将一个动态网页 伪装为静态网页
  
  为什么要伪装呢?
    e.g.: 博客园
    伪装的目的 
      - 在于增大本网站的seo查询力度 
      - 并且增加搜索引擎收藏本网站的概率
      
  搜索引擎：本质就是一个巨大的爬虫程序
  总结：无论怎么优化 还是搞不过搜索竞价
 

"""
# 实现 把url设置为.html结尾 乍一看 以为是静态(伪静态)
urlpatterns = [
    url(r'^reg.html/', views.reg, name='app01_reg'),
]
```



# 视图层

**视图函数必须返回HttpResponse对象**

```python
# 否则报错
The view app01.views.index didn't return an HttpResponse object. It returned None instead.
```

## 三板斧

```python
# HttpResponse
返回字符串类型
class HttpResponse(HttpResponseBase):
    pass

# render
返回html页面 并且返回之前还可以给html文件传值
def render(request, template_name, context=None, content_type=None, status=None, using=None):
    """
    Returns a HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)

# redirect
重定向 也是继承的HttpResponse
```

## render简单的内部原理

```python
def index(request):
    from django.template import Template, Context
    res = Template('<h1>{{ user }}</h1>')
    con = Context({'user': {'username': 'minho', 'password': 123}})
    ret = res.render(con)
    print(ret)
    return HttpResponse(ret)

>>> <h1>{&#39;username&#39;: &#39;minho&#39;, &#39;password&#39;: 123}</h1>
```

## JsonResponse

```python
"""
Q: json格式的数据有什么用?
A: 前后端数据交互需要使用到json作为过渡 实现跨语言传输数据

前端序列化：
  - JSON.stringify()      json.dumps()
  - JSON.parse()          json.loads()
"""

# 任务：给前端浏览器返回一个json格式的字符串
```

- **原生json模块实现**

```python
# 原生json模块实现
import json
def ab_json(request):
    user_dict = {'username': 'Minho这是中文', 'password': '123123', 'hobby': 'study'}
    # 先转成json格式字符串
    json_str = json.dumps(user_dict, ensure_ascii=False)
    return HttpResponse(json_str)
```

- **JsonResponse实现**

```python
from django.http import JsonResponse
def ab_json(request):
    user_dict = {'username': 'Minho这是中文', 'password': '123123', 'hobby': 'study'}
    lst = [111, 222, 333, 444]
    
    # 读源码 掌握用法 如何传参
    # 序列化 字典
    return JsonResponse(user_dict, json_dumps_params={'ensure_ascii': False})
    
    # 序列化非字典对象 需要加safe参数 通过报错信息得知
    # In order to allow non-dict objects to be serialized set the safe parameter to False
    return JsonResponse(lst, safe=False)

# JsonResponse 默认只能序列化字典 序列化其他数据(其他可以被序列化的数据 不是所有的数据都能被序列化) 需要加safe参数
```

------

## Form表单上传文件及后端如何操作

```python
"""
form表单上传文件类型的数据
  1. method必须指定成post
  2. enctype必须换成formdata
"""

def ab_file(request):
    if request.method == 'POST':
        # print(request.POST)  # 只能获取普通键值对数据 文件不行
        # print(request.FILES)  # <MultiValueDict: {'file': [<TemporaryUploadedFile: python-3.8.7-amd64.exe (application/x-msdownload)>]}>
        file_obj = request.FILES.get('file')  # 文件对象
        print(file_obj.name)
        with open(file_obj.name, 'wb') as f:
            for line in file_obj.chunks():  # 推荐加上chunks
                f.write(line)
    return render(request, 'form.html')
```

## request对象方法

```python
"""
request.method
request.POST
request.GET
request.FILES
"""

"""
request.path
request.path_info
request.get_full_path()  # 能获取完整的rul及问号后面的参数
"""
URL: http://127.0.0.1:8000/app01/ab_file/?username=minho
            
requesst.path           -> /app01/ab_file/
request.path_info       -> /app01/ab_file/

# 既能拿路由 也能拿参数 上面两个方法不能拿参数
request.get_full_path() -> /app01/ab_file/?username=minho


"""
request.body  # 原生的 浏览器发过来的二进制数据
  - GET      b''
  - POST      
"""
```

## FBV与CBV

**视图函数既可以是函数也可以是类**

### FBV

**基于函数的视图**

```python
# FBV
def index(request):
    return HttpResponse('index')
```

### CBV

- **基于类的视图**

```python
# CBV路由 urls.py写法
# as_view() CBV路由写法和FBV有点不一样(其实本质相同)
url(r'^login/', views.MyLogin.as_view()),

# 类视图
from django.views import View

class MyLogin(View):
    """只要是处理业务逻辑的视图函数 形参里面肯定要有request"""
    def get(self, request):
        return render(request, 'form.html')

    def post(self, request):
        return HttpResponse('post方法')
    
"""
FBV和CBV各有千秋

CBV特点：
  能够直接根据请求方式的不同 直接匹配到对应的方法执行
 
  思考：内部如何实现的?  -- **非常重要** 学习DRF必备的知识点
"""
```

- **CBV源码剖析**

```python
# 千万不要随意修改源码 出BUG很难找
```

**突破口**

```python
# CBV源码剖析 突破口在urls.py
from django.conf.urls import url
from app02 import views

urlpatterns = [
    url(r'^index/', views.index),
    url(r'^login/', views.LoginView.as_view()),
]
"""
函数/方法 加括号执行优先级最高 
as_view() 立即执行
猜测 as_view()可以通过我们自定义的类LoginView类直接调用 可能是下面2种情况
  - 1. @staticmethod 修饰的静态方法
  - 2. @classmethod 修饰的类方法
 
1. as_view()本质
  url(r'^login/', views.LoginView.as_view())
  上述代码在启动django的时候就会立刻执行as_view()方法
  as_view()方法会返回一个函数 view
  => url(^'^login/', views.view)  => 变形后 和FBV一样
  CBV与FBV在路由匹配上 本质是一样的 都是路由 对应 函数(函数内存地址)
  
2. 浏览器访问/login/ 触发views.view view具体做了什么? 
"""
```

**as_view()本质**

```python
@classonlymethod
def as_view(cls, **initkwargs):
    def view(request, *args, **kwargs):
        self = cls(**initkwargs)
        # cls是我们自己写的类 调用的时候自动注入
        # self = LoginView(**initkwargs) 产生一个我们自己写的类的实例
        if hasattr(self, 'get') and not hasattr(self, 'head'):
            self.head = self.get
        self.request = request
        self.args = args
        self.kwargs = kwargs
        return self.dispatch(request, *args, **kwargs)
        """
        以后 经常会需要看源码 但是在看Python源码的时候 一定要时刻提醒自己 面向对象属性方法查找顺序 mro
        总结：看源码 只要看到了self.xxx 一定要问自己 当前这个self到底是谁
        """
    return view
```

**dispatch CBV精髓**

```python
# CBV的精髓
def dispatch(self, request, *args, **kwargs):
    # Try to dispatch to the right method; if a method doesn't exist,
    # defer to the error handler. Also defer to the error handler if the
    # request method isn't on the approved list.
    # 获取当前请求的小写格式 然后比对当前请求方式是否合法
    # get请求为例
    if request.method.lower() in self.http_method_names:
       """
       反射：通过字符串来操作对象的属性或方法 运行时获取类型定义信息
       handler = getattr(自己写的类产生的对象, 'get', 当找不到get属性或者方法的时候就会用第三个参数)
       handler = 我们自己写的类里面的get方法
       """
        handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
    else:
        handler = self.http_method_not_allowed
    # 自动调用get方法
    return handler(request, *args, **kwargs)
```

**反射补充**

```python
运行时：runtime 区别于编译时 指的是程序被加载到内存中执行的时候
反射：reflection 执行是运行是获取类型定义信息

"""
一个对象能够在运行时 像照镜子一样 反射出其类型信息
简单说 在Python中 能够通过一个对象 找出其type class attribute或method的能力 成为反射或者自省
"""

具有反射能力的函数有:
  - type() 
  - isinstance()
  - callable() 
  - dir()
  - getattr() 等等...
    
反射相关的魔术方法：
__getattr__
__setattr__
__delattr__

__getattribute__  # 特殊 尽量不用
```

| 内建函数                         | 意义                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| getaddr(object, name[, default]) | 通过name返回object的属性值 当属性不存在 将使用default返回 如果没有default 则抛出AttributeError name必须为**字符串** |
| setaddr(object, name, value)     | object的属性存在则覆盖 不存在则新增                          |
| hasattr(object, name)            | 判断对象是否具有这个名字的属性 name必须为**字符串**          |




- **settings源码剖析(尝试理解)**

# 模板层

## 模板语法

### 传值

**模板语法传值**

```jinja2
{# #}    # 注释
{{ }}    # 变量相关
{% %}    # 逻辑相关
```

**后端传值示例**

```python
def index(request):
    # 模板语法可以传递的后端Python数据类型
    n = 123
    f = 11.11
    b = True
    s = 'name is minho'
    lst = ['kimi', 'huahua', 'minho']
    tup = ('11', '22', 33)
    dic = {'username': 'minho', 'age':20}
    se = {'按级别的', '密码'}
    def func():
        print('我被执行了')
        return 'func return res'
    class MyClass:
        def get_self(self):
            return 'self'
        
        @staticmethod
        def get_static():
            return 'static method'
        
        @classmethod
        def get_cls(cls):
            return 'cls'
        
        # 对象被展示到html页面上 就类似于执行了打印操作也会触发__str__方法
        def __str__(self):
            return 'MyClass obj'
    obj = MyClass()
    return render(request, 'index.html', locals())
```

**前端展示**

```jinja2
<p>整型：{{ n }} </p>
<p>浮点型：{{ f }} </p>
<p>字符串：{{ s }} </p>
<p>布尔值：{{ b }} </p>
<p>列表：{{ lst }}</p>
<p>元组：{{ tup }}</p>
<p>字典：{{ dic }}</p>
<p>集合：{{ se }}</p>
{# 传递函数名会自动加括号调用 但是模板语法不支持给函数传额外的参数 如果有参数 不执行也不报错 #}
<p>函数：{{ func }}</p>
{# 传类名的时候也会自动加括号调用(实例化) #}
<p>类：{{ MyClass }}</p>
<p>类对象(ins)：{{ obj }}</p>
{# Django模板语法 能够自动判断出当前的变量名是否可以加括号调用 如果可以就会自动执行 针对的是函数名和类名 #}
<p>普通方法：{{ obj.get_self }}</p>
<p>静态方法：{{ obj.get_static }}</p>
<p>类方法：{{ obj.get_cls }}</p>
```

### 取值

```html
django模板语法的取值 是固定的格式 只能用"句点符" .
即可点键 也可以点索引  也可以两者混用

<p>username: {{ dic.username }}</p>
<p>list取值: {{ lst.0 }}</p>
<p>info: {{ dic.hobby.3.info}}</p>
```

### 过滤器

```python
# 过滤器 就类似于是模板语法内置的 内置方法
# django内置有60多个过滤器 了解10个左右就差不多 后面遇到再去了解
```

- 基本语法

```jinja2
{{ 数据|过滤器:参数 }}
```

- 常用过滤器

```jinja2
<h1>过滤器</h1>
<p>统计长度：{{ s|length }}</p>

{# 第一个参数布尔值为True 就展示第一个参数的值 否则使用default的值 类似 dict.get(d, default) #}
<p>默认值：{{ b|default:'啥也不是' }}</p>

<p>文件大小：{{ file_size|filesizeformat }}</p>
<p>日期格式化：{{ current_time|date:'Y-m-d H:i:s' }}</p>
<p>切片操作(支持步长)：{{ lst|slice:'0:4:3' }}</p>
<p>切取字符(包含3个.)：{{ info|truncatechars:9 }}</p>
<p>切取单词(不包含3个. 只按照空格切)：{{ eng|truncatewords:5 }}</p>
<p>移除特定的字符：{{ eng|cut:'model' }}</p>
<p>拼接操作：{{ lst|join:'$' }}</p>
<p>拼接操作(加法): {{ n|add:'10' }}</p>
<p>拼接操作(加法): {{ s|add:eng }}</p>

{# hhh默认不转义标签 为了安全 不执行恶意scripts代码#}
<p>转义：{{ hhh|safe }}</p>
<p>转义：{{ sss|safe }}</p>
<p>转义：{{ res }}</p>
```

- **转义**

```python
# 模板语法 默认不转义标签 为了安全 不执行恶意scripts代码

# 转义标签用法 safe参数
# 前端
{{ var|safe }}

# 后端
from django.utils.safestring import mark_safe
res = mark_safe('<h1>新新</h1>')

"""
以后你在写全栈项目的时候 前端代码不一定非要在前端页面书写
也可以先在后端写好 然后再传递给前端页面
"""
```

### 标签

**不要被名字干扰 标签就是一堆逻辑**

```jinja2
{# for循环 #}
{% for l in lst %}
    <p>{{ forloop }}</p>
    <p>{{ item }}</p>    # 一个个元素
{% endfor %}

{# forloop需要掌握它的参数 #}
{
'parentloop': {}, 
'counter0': 0, 
'counter': 1, 
'revcounter': 6, 
'revcounter0': 5, 
'first': True, 
'last': False
}

{# if判断 #}
{% if b %}
    <p>True res</p>
{% elif s %}
    <p>elif res</p>
{% else %}
    <p>False res</p>
{% endif %}

{# for if混合使用 #}
{% for item in lst_empty %}
    {% if forloop.first %}
        <p>第一次循环</p>
    {% elif forloop.last %}
        <p>最后一次循环</p>
    {% else %}
        <p>{{ item }}</p>
    {% endif %}
    {% empty %}
        <p>for循环的可迭代对象 内部没有元素 根本没法循环</p>
{% endfor %}

{# 可以使用字典的方法 #}
{% for item in dic.keys %}
    <p>{{ item }}</p>
{% endfor %}
{% for item in dic.items %}
    <p>{{ item }}</p>
{% endfor %}
{% for value in dic.values %}
    <p>{{ value }}</p>
{% endfor %}
{% for item in lst %}
    <p>{{ item }}</p>
{% endfor %}

{# with起别名 #}
{% with dic.hobby.3.info as nb %}
    {# 在with语法内 就可以通过as后面的别名快速的使用到前面非常复杂获取数据的方式 #}
    <p>{{ nb }}</p>
{% endwith %}
```

## 自定义

**过滤器/标签及inclusion_tag**

- **自定义过滤器**

```python
"""
先三步走：
  1. 在应用下创建一个名字**必须**叫 templatetags文件夹
  2. 在该文件夹内 创建任意名称的py文件 e.g: mytag.py
  3. 在该py文件内 **必须**先书写下面两句话 (单词一个都不能错)
    form django import template
    register = template.Library()
"""

from django import template
register = template.Library()
# 自定义过滤器(参数最多只有2个)
@register.filter(name='myfilter')
def my_sum(x, y):
    return x + y

# html使用
<h1>自定义的使用(过滤器 最多只能有2个参数)</h1>
{% load mytags %}
<p>{{ n|myfilter:666 }}</p>
```

- **自定义标签**

**类似自定义函数**

```python
# 自定义标签(参数可以有多个)
@register.simple_tag(name='plus')
def index(a, b, c, d):
    return "{}-{}-{}-{}".format(a, b, c, d)

# 使用
{% load mytags %}
{# 标签多个参数之间 空格隔开 #}
<p>{% plus 'minho' 123 123 9988 %}</p>
```

- **自定义inclusion_tag**

```python
"""
内部原理：
  - 先定义一个方法
  - 在页面上调用该方法 并且可以传值
  - 该方法会生成一些数据 然后传递给一个html页面
  - 之后 将渲染好的结果放到调用的位置
  
"自动生成某一个页面的局部部分"
"""
# 自定义inclusion_tag
@register.inclusion_tag('left_menu.html')
def left(n):
    data = ['第{}项'.format(i) for i in range(n)]
    # inclusion_tag 两种给作用页面传值的方式
    # 第一种
    # return {'data': data}
    # 第二种
    return locals()  # 将data传递给left_menu.html

# 局部页面内容
<ul>
    {% for item in data %}
        <li>{{ item }}</li>
    {% endfor %}
</ul>

# 使用
"""
总结：当html页面某一个地方的页面需要传参数才能够动态的渲染出来 并且在多个页面上都需要使用到该局部 那么就考虑将该局部页面做成inclusion_tag形式
"""
{% load mytags %}
{% left 5 %}
```

## 模板继承

```python
"""
有一些网站 这些网站页面整体都大差不差 只是某一些局部在做变化
"""

# 模板的继承 你自己先选好一个你想要继承的模板页面
{% extends 'home.html' %}

# 继承了之后 子页面跟模板页面长的一摸一样的 你需要在模板页面上提前划定可以被修改的区域
{% block content %}
   模板内容
{% endblock %}

# 子页面 就可以声明想要修改哪块划定的区域
{% block content %}
  子页面内容
{% endblock %}

# 一般情况下 模板页面上应该至少有三块可以被修改的区域
# 这样划分之后 每一个子页面 就都可以有自己独有的css代码 html代码 js代码
  1. html区域
    {% block content %}
     子页面自己的html内容
    {% endblock %}
  2. css区域
    {% block css %}
      子页面自己的css
    {% endblock %}
  3. js区域
    {% block js %}
      子页面自己的js
	{% endblock %}

"""
一般情况下 模板的页面上 划定的区域越多 那么该模板的扩展性就越高
但是如果太多 那还不如自己写(没必要划分太多)
"""
```

## 模板导入

```python
"""
将页面的某一个局部 当成模块的形式
哪个地方需要就可以直接导入使用即可
"""

{% include 'ok.html' %}
```





------



# Django版本区别

```python
1.x 2.x 3.x区别
2.x 3.x 差不多
```
## 路由层

```python
"""
1. django 1.x 路由层使用的是url方法
   django 2.x 3.x 路由层使用的是path方法
    - url() 第一个参数支持正则
    - path() 第一个参数是不支持正则的 写什么就匹配什么

如果你不习惯使用path 也提供了另一个方法 re_path
from django.urls import re_path
from django.conf.urls import url  # 可以继续使用url 不推荐

2.x 3.x 里面的re_path() 等价于1.x里面的url

2. 虽然path不支持正则 但是它的内部支持5种转换器
"""
```

### path转换器

[博客参考](https://www.cnblogs.com/xiaoyuanqujing/articles/11642628.html)

- **示例**

```python
# 将第二个路由里面的内容先转成整型 然后以关键字的形式传递给后面的视图函数
path('index/<int:id>/', views.index)
```

- **5种转换器**

```python
# 5种转换器
str    匹配除了路径分隔符（/）之外的非空字符串，这是默认的形式
int    匹配正整数，包含0
slug   匹配字母、数字以及横杠、下划线组成的字符串
uuid   匹配格式化的uuid，如 075194d3-6885-417e-a8a8-6c931e272f00
path   匹配任何非空字符串 包含了路径分隔符（/）（不能用？）
```

- **自定义转换器**

```python
# 除了有默认的5个转换器之外 还支持自定义转换器
class MonthConverter:
    regex='\d{2}' # 属性名必须为regex
    
    def to_python(self, value):
        return int(value)
    
    def to_url(self, value):
        # 匹配的regex是两个数字，返回的结果也必须是两个数字
        return value 
```

- **自动转换器使用**

```python
from django.urls import path,register_converter
from app01.path_converts import MonthConverter

# 注册
register_converter(MonthConverter,'mon')  

# 使用
from app01 import views
urlpatterns = [
    path('articles/<int:year>/<mon:month>/<slug:other>/', views.article_detail, name='aaa'),
]
```

## 模型层

```python
"""
模型层里面1.x外键默认都是级联更新删除的
但是到了2.x和3.x中 需要你自己手动配置on_delete参数
"""

# 1.x
publish = models.ForeignKey(to='Publish')

# 2.x 3.x
# 具体参数参考官网文档
publish = models.ForeignKey(to='Publish', on_delete='xxx')
```

**on_delete参数补充**

[ForeignKey.on_delete](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.ForeignKey.on_delete)

```python
on_delete=models.CASCADE,     # 删除关联数据,与之关联也删除
on_delete=models.DO_NOTHING,  # 删除关联数据,什么也不做
on_delete=models.PROTECT,     # 删除关联数据,引发错误ProtectedError
on_delete=models.SET_NULL,    # 删除关联数据,与之关联的值设置为null（前提该字段需要设置为可空,一对一同理）
on_delete=models.SET_DEFAULT, # 删除关联数据,与之关联的值设置为默认值（前提FK字段需要设置默认值,一对一同理）
on_delete=models.SET(),       # 删除之后执行一个函数
on_delete=RESTRICT,           # New in Django 3.1.
```

