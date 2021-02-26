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

[DjangoRESTFramework官网](https://www.django-rest-framework.org/)

[Django REST framework 中文教程](https://www.w3cschool.cn/lxraw/)

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

# 序列化器 Serializer

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

### 简单使用

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
        book = Book.objects.filter(pk=pk).first()
        # 用一个类 毫无疑问：实例化 
        # instance参数关键字传参或者第一个位置位置传参(源码)
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

### 字段选项

```python
# 检验功能 非常类似forms组件
  1. 自带字段选项校验(更多参考字段链接详细内容)
     - read_only
     - required  默认为True
     - max_length 
       ...        
  2. 局部钩子函数
  3. 全局钩子函数
```

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
    def get(self, request, pk):
		...

    def put(self, request, pk):
        response_msg = {'status':100, 'msg':'成功'}
        # 找到这个对象
        book = Book.objects.filter(pk=pk).first()
        # 直接用request.data的数据来修改原来的对象
        
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

