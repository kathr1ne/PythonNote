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

# 注意！！！
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
on_delete-models.SET(),      # 删除之后执行一个函数
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



------

# Django路由层

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
注意: 别名唯一 别名不能冲突！！！
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

**总结：其实只要保证名字不冲突 就没有必要使用名称空间 **

```python
"""
一般情况下 有多个app的时候 我们在起别名的时候 会加上对应app的前缀
这样的话 就能够确保多个app之前名字不冲突的问题
"""
```

## 伪静态

**了解**

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

