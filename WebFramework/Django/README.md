# Django

[Django英文官方文档](https://docs.djangoproject.com/en/3.1/)

[Django中文官方文档](https://docs.djangoproject.com/zh-hans/3.1/)

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

# 低版本 命令行创建 需要配置模板目录
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

# 静态文件配置 列表 可以有多个
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

### request对象方法初始

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
# 
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

**创建model类**

```python
# 去应用下面的 models.py 文件
from django.db import models
# Create your models here.
# model类 编写
class User(models.Model):
    id = models.AutoField(primary_key=True)  # id int primary_key auto_increment
    username = models.CharField(max_length=32)  # username varchar(32)
    password = models.IntegerField()  # password int

    
# 数据库迁移命令
# 只要你修改了models.py中跟 **数据库** 相关的代码 就必须重新执行下面两条命令
python manage.py makemigrations    # 将操作记录基础出来(migrations文件夹)
python manage.py migrate  # 将操作真正的同步到数据库中


# 设置sql_mode官方文档参考
https://docs.djangoproject.com/zh-hans/3.1/ref/databases/#mysql-notes
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

# DjangoRestFramework

[Django REST Framework文档](https://www.django-rest-framework.org/)

