# RESTFramework

[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

[DjangoRESTFramework官网](https://www.django-rest-framework.org/)

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
GET /collection：返回资源对象的列表(数组)
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
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

11. **其他**

```python
1. API的身份认证应该使用OAuth 2.0框架

2. 服务器返回的数据格式 应该尽量使用JSON 避免使用XML
```

## 序列化

```python

```

