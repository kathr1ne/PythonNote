---
title: DjangoRESTframework
date: 2021-02-22 23:00:00
categories: 
- PYTHON
tags:
- Python
- Web
- 前后端分离
- RESTful
- API
---

# RESTFramework

[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

[DjangoRESTframework官网](https://www.django-rest-framework.org/)

[DjangoRESTframework 中文教程](https://www.w3cschool.cn/lxraw/)

## RESTful API

API接口： 通过网络 规定了前后台信息交互规则的url链接 也就是前后台信息交互的**媒介**

### 简介

```python
REST 全称：Representational State Transfer 中文意思的表述(通常译为：表征性状态转移)

RESTful是一种定义Web API接口的设计风格 尤其适用于前后端分离的应用模式中
这种风格的理念认为：后端开发任务就是提供数据的 对外提供的是数据资源的访问接口 所以在定义接口时 客户端访问的URL路径就表示这种要操作的数据资源

事实上 我们可以使用任何一个框架都可以实现符合restful规范的接口
```

### 规范

***Restful 10条规范***

1. **协议**

```python
URL链接一般都采用https协议进行传输 注：采用https协议 可以提高数据交互过程中的安全性
```

2. **接口特征性表现(一看就知道是API接口)**

   用api关键字表示接口url

```python
- https://api.example.com
- https://example.org/api/      
# 如果确定API很简单 不会有进一步扩展 可以考虑放在主域名之下
```

3. **多版本共存**

   应该将API的版本放入URL(一种资源有多版本请况下)

```python
- https://api.example.com/v1/
- https://api.example.com/v2/

# 另一种做法是 将版本号放在HTTP头信息中 但不如放入URL方便和直观
```

4. **数据即是资源 均使用名字(复数) - 重要**

   接口一般都是完成前后台数据的交互 交互的数据我们称之为资源

```python
- https://api.example.com/v1/zoos
- https://api.example.com/v1/animals
- https://api.example.com/v1/employees
    
注：一般提倡用资源的复数形式 在URL链接中不要出现操作资源的动词
  
# 特殊的接口可以出现动词 因为这些接口一般没有一个明确的资源 或是动词就是接口的核心含义
  - https://api.baidu.com/place/search
  - https://api.baidu.com/login
```

5. **资源操作由请求方式决定(method) - 重要**

   操作资源一般都会设计到增删改查 我们提供请求方式来标识这些动作

```python
- https://api.baidu.com/books  - GET请求：获取所有书
- https://api.baidu.com/books/1  GET请求：获取主键为1的书
- https://api.baidu.com/books    POST请求：新增一本书
- https://api.baidu.com/books/1  PUT请求：整体修改主键为1的书
- https://api.baidu.com/books/1  PATCH请求：局部修改主键为1的书
- https://api.baidu.com/books    DELETE请求：删除主键为1的书
```

   常用的HTTP动词有下面五个(括号里面是对应的SQL命令)

```python
- GET(SELECT):    从服务器取出资源(一项或多项资源)
- POST(CREATE):   在服务器新建一个资源
- PUT(UPDATE):    在服务器更新资源(客户端提供改变后的完整资源)
- PATCH(UPDATE):  在服务器更新资源(客户端提供改变的属性)
- DELETE(DELETE): 从服务器删除资源
```

   还有两个不常用的HTTP动词

```python
- HEAD: 获取资源的元数据
- OPTIONS: 获取信息 关于资源的哪些属性是客户端可以改变的
```

6. **过滤信息**

```python
# 如果记录数量很多 服务器不可能都将它们返回给用户
# API应该提供参数 过滤返回结果
  - ?limit=10   指定返回记录的数量
  - ?offset=10  指定返回记录的开始位置
  - ?page=2&per_page=100  指定第几页，以及每页的记录数
  - ?sortby=name&order=asc  指定返回结果按照哪个属性排序，以及排序顺序
  - ?animal_type_id=1  指定筛选条件
```

7. **状态码**

   [状态码完全列表](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

   服务器向用户返回的状态码和提示信息

```python
# 常见的有以下一些(方括号中是该状态码对应的HTTP动词)
200 OK - [GET]：服务器成功返回用户请求的数据 该操作是幂等的（Idempotent）
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功

301 Moved Permanently 永久重定向
302 Found 暂时重定向

400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误 服务器没有进行新建或修改数据的操作 该操作是幂等的
401 Unauthorized - [*]：表示用户没有权限(令牌、用户名、密码错误)
403 Forbidden - [*] 表示用户得到授权(与401错误相对) 但是访问是被禁止的
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录 服务器没有进行操作 该操作是幂等的
406 Not Acceptable - [GET]：用户请求的格式不可(比如用户请求JSON格式 但是只有XML格式)
410 Gone -[GET]：用户请求的资源被永久删除 且不会再得到的
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时 发生一个验证错误

500 INTERNAL SERVER ERROR - [*]：服务器发生错误 用户将无法判断发出的请求是否成功
```

8. **错误处理**

```python
# 如果状态码是4xx 就应该向用户返回出错信息
# 一般来说 返回的信息中将error作为键名 出错信息作为键值即可
{
    error: "Invalid API key"
}
```

9. **返回结果**

   针对不同操作 服务器向用户返回的结果应该符合以下规范

```python
GET    /collection： 返回资源对象的列表(数组)
GET    /collection/resource： 返回单个资源对象
POST   /collection： 返回新生成的资源对象
PUT    /collection/resource： 返回完整的资源对象
PATCH  /collection/resource： 返回完整的资源对象
DELETE /collection/resource： 返回一个空文档
```

10. **Hypermedia API**

```python
RESTful API最好做到Hypermedia 
即返回结果中提供链接 连向其他API方法 使得用户不查文档 也知道下一步应该做什么

Hypermedia API的设计被称为HATEOAS
Github的API就是这种设计 访问api.github.com会得到一个所有可用API的网址列表
{
  "current_user_url": "https://api.github.com/user",
  "authorizations_url": "https://api.github.com/authorizations",
  // ...
}
```

**其他**

```python
1. API的身份认证应该使用OAuth 2.0框架

2. 服务器返回的数据格式 应该尽量使用JSON 避免使用XML
```

# DRF初识
## 安装

```python
pip install djangorestframework==3.12.2
```

## 简单使用

```python
1. 在settings.py注册app
INSTALLED_APPS = [
     ...
    'rest_framework',
]

2. 在models.py中写表模型
from django.db import models

class Book(models.Model):
    nid = models.AutoField(primary_key=True)
    name = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=5, decimal_places=2)
    author = models.CharField(max_length=32)
    
3. 新建一个序列化类
from rest_framework.serializers import ModelSerializer
from .models import Book

class BookModelSerializer(ModelSerializer):
    class Meta:
        model = Book
        fields = "__all__"
        
4. 在视图中写视图类(CBV)
from rest_framework.viewsets import ModelViewSet
from .models import Book
from .serializer import BookModelSerializer

class BooksViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookModelSerializer
    
5. 写路由关系
from django.contrib import admin
from django.urls import path
from .book import views
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register('books', views.BooksViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
]

urlpatterns += router.urls

"""
DRF通过上面几步很少的代码 就已经实现了/books/的5个接口：
127.0.0.1:8000/books/    GET    获取所有书籍
127.0.0.1:8000/books/    POST   新增书籍
127.0.0.1:8000/books/2/  GET    获取主键为2的书籍详情
127.0.0.1:8000/books/2/  DELETE 删除主键为2的书籍
127.0.0.1:8000/books/2/  PUT    修改主键为2的书籍详情
"""
```

## CBV源码

```python
# ModelViewSet继承自View(django远程的View)
# ModelViewSet -> APIView -> View
```

### View

- **CBV简单实现**

```python
# urls.py
urlpatterns = [
    path('books/', views.Books.as_view()),
]

# views.py
from django.views import View
from django.http import JsonResponse

class Books(View):
    def get(self, request):
        return JsonResponse({'c++': 111})
    
"""
127.0.0.1:8000/books/  GET  通过CBV实现GET请求方法
"""
```

- **as_view()**

```python
"""
源码解读：切入口：as_view()
本质还是FBV: 
    views.Books.as_view() ->  as_view()返回一个函数对象 -> views.Books.view
    
如果请求过来 路径匹配上 会执行 views.Books.view的 view内存地址指向的函数对象 并把request参数传入(当次请求的request对象) -> view(request) 
"""

# as_view()本质
@classonlymethod
def as_view(cls, **initkwargs):
    def view(request, *args, **kwargs):
        self = cls(**initkwargs)
        # cls是我们自己写的类 调用的时候自动注入(谁调用 注入谁)
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

# CBV的精髓 dispath
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

### APIView

- **CBV简单实现**

```python
# urls.py
# 也是as_view() 只不过被APIView(继承View)重写
path('books_apiview/', views.BooksAPIView.as_view())

# views.py
from rest_framework.views import APIView

class BooksAPIView(APIView):
    def get(self, request):
        return JsonResponse({'APIView': 666})
```

- **as_view()**

```python
"""
源码解读：切入口还是 as_view() -> APIView的as_view() -> view 函数内存地址
"""
@classmethod  # 类的绑定方法
def as_view(cls, **initkwargs):
    view = super().as_view(**initkwargs)  # 调用父类的as_view方法
    view.cls = cls
    view.initkwargs = initkwargs
    # 继承APIView的视图类会禁用csrf认证(drf会有自己的认证)
    # 添加装饰器的另一种方法 @方式是语法糖 本质就是传入一个被装饰函数给装饰器函数当作实参
    return csrf_exempt(view)

# 使用csrf装饰器 URL视图还可以这么写 
path('test/', csrf_exempt(as_view))

"""
发起请求 -> 路由匹配成功 -> view(request) -> 调用dispath() -> mro属性查找顺序 self.dispath会执行到APIView的dispath方法 而不再走View类的dispath -> 把请求方法转为小写 -> 通过反射去对象中查找 有没有改方法定义的属性 有则传入request并执行
"""

# APIView的disapth
def dispatch(self, request, *args, **kwargs):
    self.args = args
    self.kwargs = kwargs
    # request参数是当次请求的request对象
    # 请求过来 wsgi注入一个envrion字段 django把它包装成一个request对象
    
    # 然后重新赋值的request 是初始化处理后的一个新的Request对象
    request = self.initialize_request(request, *args, **kwargs)
    
    # 现在视图函数拿到的request 已经不是django原生给我们封装的request
    # 而是drf自己定义的request对象(mro) 以后视图函数再使用的request对象 就是这个新的drf定义的对象
    self.request = request
    self.headers = self.default_response_headers  # deprecate?

    try:
        # 三大认证模块
        self.initial(request, *args, **kwargs)

        # Get the appropriate handler method
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(),
                              self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
		 # 响应模块
        response = handler(request, *args, **kwargs)

    except Exception as exc:
       # 异常模块
        response = self.handle_exception(exc)
	
    # 渲染模块
    self.response = self.finalize_response(request, response, *args, **kwargs)
    return self.response

# 封装drf自己的request对象
def initialize_request(self, request, *args, **kwargs):
    parser_context = self.get_parser_context(request)
    # django原来的request 被封装到Request里面 
    # self._request 原生的request
    # self.request  drf封装的request
    
    # 返回DRF封装的Request类实例化之后的对象
    return Request(
        request,    # 原生的request对象
        # 获取解析类
        parsers=self.get_parsers(),
        authenticators=self.get_authenticators(),
        negotiator=self.get_content_negotiator(),
        parser_context=parser_context
    ) 
```

- **Request**

```python
"""
from rest_framework.request import Request

只要继承了APIView 视图类中的request对象 都是新的
也就是上面导入路径这个Request的对象(实例)

使用使用新的request对象 就像使用之前的request是一模一样的(因为重写了__getattr__方法)

def __getattr__(self, attr):
    try:
        return getattr(self._request, attr)
    except AttributeError:
        return self.__getattribute__(attr)
"""

class Request:
    """
    Wrapper allowing to enhance a standard `HttpRequest` instance.

    Kwargs:
        - request(HttpRequest). The original request instance.
        - parsers(list/tuple). The parsers to use for parsing the
          request content.
        - authenticators(list/tuple). The authenticators used to try
          authenticating the request's user.
    """

    def __init__(self, request, parsers=None, authenticators=None,
                 negotiator=None, parser_context=None):
        assert isinstance(request, HttpRequest), (
            'The `request` argument must be an instance of '
            '`django.http.HttpRequest`, not `{}.{}`.'
            .format(request.__class__.__module__, request.__class__.__name__)
        )
        # self._request 原生的request
        self._request = request
        self._data = Empty
		...
        
        
# restframework封装的request对象
"""
django封装的request对象没有.data {}
它是一个字典 
  - post请求不管使用什么编码(form/urlencoded/json) 传过来的数据 都在request.data
  - get请求
"""
request.data    
```

- **取POST请求数据**

|            | django原生request(request.POST) | drf封装后的Request对象(request.data) |
| ---------- | ------------------------------- | ------------------------------------ |
| form       | 有数据 QueryDict                | 有数据 QueryDict                     |
| urlencoded | 有数据 QueryDict                | 有数据 QueryDict                     |
| json       | 无数据                          | 有数据 普通字典                      |

- **取GET请求url中的数据**

```python
# drf Request类方法
# self._request.GET = request.query_params
# django原来取： request.GET
# drf取： request.query_params

@property
def query_params(self):
    return self._request.GET
```

- **APIView的initial方法**

```python
# dispath中调用 initial方法
# request参数 已经是新的request
def initial(self, request, *args, **kwargs):
	...
    # Ensure that the incoming request is permitted
    """
    perform_authentication
    认证组件：检验用户 - 游客、合法用户、非法用户
      游客： 代表检验通过 直接进入下一步校验(权限检验)
      合法用户：代表校验通过 将用户存储再request.user中 再进入下一步校验(权限校验)
      非法用户：代表校验失败 抛出异常 返回403权限异常结果
    """
    self.perform_authentication(request)
    """
    check_permissions
    权限组件：校验用户权限 - 必须登录、所有用户、登录读写游客只读、自定义用户角色
      认证通过：可以进入下一步校验(频率认证)
      认证失败：抛出异常 返回403权限异常结构
    """
    self.check_permissions(request)
    
    """
    check_throttles
    频率组件：限制视图接口被访问的频率次数 - 限制的条件(IP/id/唯一键)、频率周期时间(s/m/h)、频率的次数(3/s)
    没有达到限制：正常访问接口
    达到限制：限制时间之内不能访问 限制时间达到后 可以重新访问
    """
    self.check_throttles(request)
```

- **补充**

```python 
def add(x, y):
    return x + y

# 由于Python中一切皆对象 所以都可以设置属性 取出属性
# Python源码中使用非常多
add.xyz = 'xyz111'  

print(add(5, 5))
print(add.xyz)
```

# 序列化器

 **Serializer**

```python
"""
序列化：序列化器会把模型对象转换为字典 经过response以后变成json字符串 
       model对象 -> json
       
反序列化：把客户端发送过来的数据 经过request以后变成字典(框架封装) 序列化器可以把字典转成模型
       json -> model对象
       反序列化另一个作用：完成数据的校验功能(类似forms组件)
"""
```

## 序列化

### 查询一个数据

```python
"""
GET 127.0.0.1:8000/books/1 获取书籍id为1的书籍信息
  1. 写一个序列化的类 继承Serializer类
  2. 在类中写想要序列化的字段 想序列化哪个字段 就在类中写哪个字段
  3. 在视图类中使用 导入自定义序列化类 -> 实例化得到的序列化类对象(把要序列化的对象传入)
  4. 序列化类的对象.data 是一个字典
  5. 把字典返回 如果不使用rest_framework提供的Response 就得使用JsonResponse
"""

# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('books/<int:pk>', views.BooksDetailView.as_view()),
]

# serializers.py
# from rest_framework.serializers import Serializer
from rest_framework import serializers

class BookSerializer(serializers.Serializer):
    """想要序列化的字段 不需要直接注释即可"""
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField()
    price = serializers.DecimalField(max_digits=5, decimal_places=2)
    author = serializers.CharField()
    publish = serializers.CharField()

# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from django.http import JsonResponse

from .models import Book
from .serializers import BookSerializer

class BooksDetailView(APIView):
    def get(self, request, pk):
        # 获取数据对象
        book = Book.objects.filter(pk=pk).first()
        # 用一个类 毫无疑问：实例化 
        # instance参数关键字传参或者第一个位置位置传参(源码) -> 序列化
        serializer = BookSerializer(instance=book)
        # serializer.data -> 序列化后的字典
        print(serializer.data)
        """
        Response
          使用drf Response返回 需要在settings.py注册rest_framework
        Response会判断访问来源(user-agent) 来返回 渲染好的网页 或者是 json数据
        
        JsonResponse
          无需再注册rest_framework
          只会返回json数据
        """
        return Response(serializer.data)
        # return JsonResponse(serializer.data)
```

### 字段类型

[SerializerFields](https://www.django-rest-framework.org/api-guide/fields/)

[序列化器字段](https://www.w3cschool.cn/lxraw/lxraw-u4kq35ot.html)

```python
# 完整字段见上面参考链接
CharField
IntergerField
DateField
...
```

### 字段参数

```python
# 检验功能 非常类似forms组件
  1. 自带字段选项校验(更多参考字段链接详细内容)
     - read_only
     - required  默认为True
     - max_length 
     - validators
         验证器功能列表 应将其应用于输入字段输入 并引发验证错误或简单地返回
       ...        
  2. 局部钩子函数
  3. 全局钩子函数
    
# 通用字段参数
read_only        表明该字段仅用于序列化输出 默认False
write_only       表明该字段仅用于反序列化输入 默认False
required         表明该字段在反序列化时必须输入 默认True
default          反序列化时使用的默认值
allow_null       表明该字段是否允许传入None 默认Flase
validators       该字段使用的验证器
error_messages   包含错误编号与错误信息的字典
label            用于HTML展示API页面时 显示的字段名称
help_text        用于HTML展示API页面时 显示的字段帮助提示信息
```

## 反序列化

### 修改数据

```python
"""
PUT 127.0.0.1:8000/books/1 修改书籍主键为1的书籍信息
  1. 写一个序列化的类 继承Serializer类
  2. 在类中写想要反序列化的字段
  3. 在视图类中使用 导入自定义序列化类 -> 实例化得到的序列化类对象(把要修改的对象 和 修改的数据传入)
      # instance=要修改的对象
      # data=修改的数据
      serializer = BookSerializer(instance=book, data=request.data)
   
   4. 数据校验 
      serializer.is_valid()
      4.1 如果校验通过 就保存
          serializer.save()  # 是序列化器的save()方法
      4.2 如果校验不通过
          返回错误信息
          
   5. 如果字段的校验规则不够 可以自己写钩子函数(局部和全局 类似forms)
"""

class BooksDetailView(APIView):
    def put(self, request, pk):
        response_msg = {'status':100, 'msg':'成功'}
        # 找到这个对象
        book = Book.objects.filter(pk=pk).first()
        # 直接用request.data的数据来修改原来的对象 -> 反序列化
        serializer = BookSerializer(book, request.data)
        # 一定要数据验证(类似form表单验证)
        if serializer.is_valid():
            # 直接调用报错 需要重写update方法 接口规范了子类的行为 鸭子类型
            # NotImplementedError: `update()` must be implemented.
            serializer.save()  # 验证通过则返回
            response_msg['data'] = serializer.data
            # return Response(serializer.data)
        else:
            response_msg['status'] = 1001
            response_msg['msg'] = '数据校验失败'
            response_msg['data'] = serializer.errors
        return Response(response_msg)
    
# 直接继承Serializer没有update方法 需要重写
class BookSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField(max_length=16, min_length=4)
    price = serializers.DecimalField(max_digits=5, decimal_places=2)
    author = serializers.CharField()
    publish = serializers.CharField()

    def update(self, instance, validated_data):
        # instance是book这个对象
        # validated_data是校验后的数据
        instance.name = validated_data.get('name')
        instance.price = validated_data.get('price')
        instance.author = validated_data.get('author')
        instance.publish = validated_data.get('publish')
        instance.save()  # django ORM提供的
        return instance
```

### 数据校验钩子函数

- **局部钩子**

```python
from rest_framework import serializers
from rest_framework.exceptions import ValidationError

class BookSerializer(serializers.Serializer):
    ...

    def validate_price(self, data):
        """
        validate_字段名(通过反射获取该方法) 接收一个参数
        :param data: 就是price 可以对任意单一字段自定义校验
        :return: data
        """
        # print(data)
        # print(type(data))
        if data > 10:
            return data
        # 校验失败 抛出异常
        raise ValidationError('价格太低')
        
========================
{
    "status": 1001,
    "msg": "数据校验失败",
    "data": {
        "price": [
            "价格太低"
        ]
    }
}
```

- **全局钩子**

```python
from rest_framework import serializers
from rest_framework.exceptions import ValidationError

class BookSerializer(serializers.Serializer):
    ...
    
    def validate(self, attrs):
        """
        全局钩子：校验多个字段
        :param attrs: 校验通过的数据(validated_data)
        :return: attrs 
        """
        print(attrs)
        author = attrs.get('author')
        publish = attrs.get('publish')
        if author == publish:
            raise ValidationError('作者名字和出版社一致')
        return attrs
===============================    
{
    "status": 1001,
    "msg": "数据校验失败",
    "data": {
        "non_field_errors": [
            "作者名字和出版社一致"
        ]
    }
}
```

- **参数validators(非钩子函数 字段选项验证器 较少使用)**

```python
def check_author(data):
    if data.startswith('sb'):
        raise ValidationError('作者名字不能以sb开头')
    return data

class BookSerializer(serializers.Serializer):
    ...
    # 使用字段参数：验证器
    author = serializers.CharField(validators=[check_author])
=============================
{
    "status": 100,
    "msg": "数据校验失败",
    "data": {
        "author": [
            "作者名字不能以sb开头"
        ]
    }
}

"""
加上上面的钩子函数和自带的字段验证 总共有三种验证数据的方式：
  1. 字段参数自带的验证参数们
  2. 局部钩子/全局钩子函数
  3. 参数：validators 验证器
  
抛出无效数据的异常
  .is_valid()方法使用可选的raise_exception标志 如果存在验证错误将会抛出一个serializers.ValidationError异常
  这些异常由REST framework提供的默认异常处理程序自动处理 
  默认情况下将返回HTTP 400 Bad Request响应
"""

# 如果数据无效就返回400响应
serializer.is_valid(raise_exception=True)
```

### 序列化反序列化字段处理

```python
"""
问题：我们序列化的时候和反序列化的时候 使用的字段数目不全部一致
     比如主键id 序列化展示 反序列化不应该输入

解决：
  1. 写两个serializer类 一个用于序列化 一个用于反序列化 -> 冗余度非常高 麻烦
  2. 字段参数解决
      read_only    表明该字段仅用于序列化输出 默认False
      write_only   表名该字段仅用于反序列化输入 默认False
"""
class BookSerializer(serializers.Serializer):
    """
    read_only=True
      - 序列化输出 有该字段
      - 反序列化传入数据 不需要传该字段 错误的输入所有的read_only字段都将被忽略
    write_only
      - 序列化输出 没有该字段
      - 反序列化传入数据 必须传入该字段 否则校验失败
    """
    id = serializers.IntegerField(read_only=True)
    publish = serializers.CharField(max_length=64, min_length=2, write_only=True)
```

## 其余API操作
### 查询所有数据

```python
# URL
path('books/', views.Books.as_view())

# View
class Books(APIView):
    def get(self, request):
        books = Book.objects.all()
        # 序列化多条 需要加参数：many=True
        serializer = BookModelSerializer(instance=books, many=True)
        return Response(serializer.data)
```

### 新增数据

```python
# URL
path('books/', views.Books.as_view())

# models.py
class Books(APIView):
    def post(self, repost):
        # 修改才有instance 新增没有instance 只有data
        # 必须关键字传参 位置传参会给到第一个位置参数:instance
        book_ser = BookModelSerializer(data=repost.data)
        # 校验字段
        if book_ser.is_valid():
            book_ser.save()
            return Response(book_ser.data)
        return Response(book_ser.errors)
    
# serializers.py 需要重写create()方法
class BookSerializer(serializers.Serializer):
    def create(self, validated_data):
        """
        :param validated_data: dict正好可以**解构
        :return: 
        """
        return Book.objects.create(**validated_data)
```

### 删除数据

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Book

# 删除无需序列化器
class BooksDetail(APIView):
    def delete(self, request, pk):
        """删除一个数据"""
        Book.objects.filter(pk=pk).delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

## 自定义响应内容

```python
# utils.py
class MyResponse:
    def __init__(self):
        self.status = 100
        self.mgs = 'success'

    @property
    def get_dict(self):
        return self.__dict__

# 使用的时候 实例化再修改属性即可
if __name__ == '__main__':
    res = MyResponse()
    res.status = 101
    res.msg = '数据校验失败'
    res.data = {'name': 'Minho'}
    print(res.get_dict)
```

## 模型类序列化器

```python
class BookModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book                  # 对应models.py中的表模型(要序列化哪个表的数据)
        # fields = '__all__'          # __all__标识序列化所有字段
        # fields = ['name', 'price']  # 只序列化指定字段
        exclude = ['name']            # 跟fields不能都写 写哪个字段标识排除哪个字段
        read_only_fields = ['price']
        extra_kwargs = {
            'author': {'write_only': True}
        }

"""
write_only_fields 弃用 -> 使用extra_kwargs解决
  可以通过使用extra_kwargs选项快捷地在字段上指定任意附加的关键字参数
  类似于：Serializer的 name = serializers.CharField(max_length=16, min_length=4)
  
示例：
  extra_kwargs = {'password': {'write_only': True},
                  'author': {'write_only': True},}
                  
备注：
  继承ModelSerializer之后 不用再自己重写 .update() 和 .create()方法
  其他使用方式和继承Serializer 一摸一样
"""        
```

## many=True源码分析

```python
# 序列化多条数据的时候 需要传 many=True
class Books(APIView):
    def get(self, request):
        # 序列化单条
        book = Book.objects.all()
        books_one_ser = BookModelSerializer(instance=book)
        # 序列化多条
        books = Book.objects.all()
        books_ser = BookModelSerializer(instance=books, many=True)
        print(type(books_one_ser))
        print(type(books_ser))
        return Response(books_ser.data)
 
# many=True时 返回的结果跟不传 不是同一个类的对象
单条类型： <class 'drftutorial.book.serializer.BookModelSerializer'>
多条类型： <class 'rest_framework.serializers.ListSerializer'> 

"""
对象的生成：先调用类的__new__方法 生成空对象
实例化：类名(参数) 调用类的__init__()方法

类的__new__方法 控制对象的生成 -> 由此猜测BookModelSerializer类的__new__方法做了处理 根据是否有many=True参数 生成不同的类对象
"""
class BaseSerializer(Field):
    ...
    def __new__(cls, *args, **kwargs):
        # We override this method in order to automatically create
        # `ListSerializer` classes instead when `many=True` is set.
        if kwargs.pop('many', False):
            return cls.many_init(*args, **kwargs)
        # 没有传many=True 正常实例化
        return super().__new__(cls, *args, **kwargs)
    
    @classmethod
    def many_init(cls, *args, **kwargs):
		...
        """
        如果传入many=True 调用该方法 生成新得类对象 里面就是一个个得Serializer对象
        """
        return list_serializer_class(*args, **list_kwargs)
```

## Serializer高级用法

[关联字段](https://www.django-rest-framework.org/api-guide/relations/)

### 一对多关系字段

- **source参数**

```python
source参数就是指定序列化对象的属性
该属性可以是模型类的字段属性
也可以是该模型类的方法(类方法本质也是类属性 本质一样）
            
"""
source的使用 下面三个功能：
  1. 可以改字段名字  xxx = serializers.CharField(max_length=32, source='title')
  2. 可以.跨表取属性 publish = serializers.CharField(source='publish.email')
  3. 可以执行方法 publish_date = serializers.DateTimeField(source='test')  # test不是model类对应字段 而是自定义函数
"""
```

**source=对象属性**

```python
"""
一对多关系字段：
  1. 模型类__str__魔术方法
     publish = serializers.CharField() -> book.publish -> 获取publish的__str__方法返回值
  
  2. source参数用法
     publish = serializers.CharField(source='publish.email')
     source参数可以直接取模型类的字段
       2.1 更换序列化器显示的字段名称(隐藏真正的数据库字段) 用source与实际的数据库模块字段对应 
       xxx = serializers.CharField(max_length=32, source='title')       
       2.2 一对多关系中 可以取到关联关系表中的其他字段(跨表)
       publish = serializers.CharField(source='publish.email')
"""

from rest_framework import serializers

class BookSerializer(serializers.Serializer):
    xxx = serializers.CharField(max_length=32, source='title')
    price = serializers.DecimalField(max_digits=8, decimal_places=2)
    publish_date = serializers.DateTimeField()
    # book.publish 外键关联字段 直接写 显示结果会显示__str__方法的结果
    publish = serializers.CharField(source='publish.email')
    # book.authors None -> book.authors.all()   
```

**source=对象方法**

```python
# serializers.py
from rest_framework import serializers

class BookSerializer(serializers.Serializer):
    xxx = serializers.CharField(max_length=32, source='title')
    publish_date = serializers.DateTimeField(source='test')
    
# models.py
class Book(models.Model):
    title = models.CharField(max_length=32)
    
    def test(self):
        return 'book.test method'

"""
source指定的方法return内容就是序列化的内容
"""
```

**结果示例**

```json
{
    "xxx": "红楼梦",
    "publish_date": "book.test method",
}
```



### 多对多关系字段

- **SerializerMethodField**

```python
from rest_framework import serializers

class BookSerializer(serializers.Serializer):
	...
    authors = serializers.SerializerMethodField()

    def get_authors(self, obj):
        # obj: book对象
        authors = obj.authors.all()  # 跨表拿出所有作者
        return [{'name': author.name, 'age': author.age} for author in authors]

"""
serializer通过字段取出多对多关系表数据：
  SerializerMethodField(method_name=None)
  这是一个只读字段 它通过在附加的序列化器类上调用一个方法来获取其值 它可以用于将任何类型的数据添加到对象的序列化表示中
  该字段会绑定一个方法 如果没有指定method_name  则默认为：get_<field_name>(self, obj): pass
  该方法返回什么 序列化的字段就显示什么内容
"""
```

**结果示例**

```python
{
    "xxx": "红楼梦",
    "price": "123.45",
    "publish_date": "2021-01-05T20:43:26Z",
    "publish": "南京出版社",
    "authors": [
        {
            "name": "minho",
            "age": 25
        },
        {
            "name": "kimi",
            "age": 46
        }
    ]
}
```





# 视图组件

```python

```



# 解析器

```python

```



# 认证组件

```python

```



# 权限组件

```python

```



# 频率组件

```python

```



# 分页器 响应器

```python

```



# url控制器 版本控制

```python

```

