# Django

[Django英文官方文档](https://docs.djangoproject.com/en/3.1/)

[Django中文官方文档](https://docs.djangoproject.com/zh-hans/3.1/)

## Install

```bash
# 安装django == 指定安装版本
pip install django==x.x.x  
```



## 本质

```bash
# Django本质
django本身不是Server(不是 nginx|httpd) 只是个WSGI App 
需要配合Server调用它（dango本质 只是个大函数 调用者就是WSGI Server）
浏览器发起一个HTTP request报文 到TCP
Server默认监听80 只不过这个server正好它能提供http请求
```



## Useage

```bash
# 使用脚手架工具 创建项目相关文件
django-admin startproject name [dir]  

django-admin startproject cmdb .

python manage.py startapp user  
# 创建一个业务包 项目中的一个应用 一个功能模块
# 创建包之后 一定要在 项目settings.py文件中 注册一下：配置 INSTALLED_APPS = [..., 'user',]   非常重要


# 目录结构
WEBTest  # 项目目录
  -- first  # 主目录 主要是项目配置、入口
    -- __init__.py  # 包文件
    -- asgi.py  # 2.x 不支持asgi 各种gi的入口文件
    -- settings.py  # 全局配置文件 重要
    -- urls.py  # 路径映射
    -- wsgi.py  # 入口
  
  -- user  # 和fisrt平行 功能模块
    -- migrations  # 数据库迁移相关的目录
      -- __init__.py
    -- __init__.py  # 包文件
    -- admin.py  # 后台管理
    -- apps.py  # 跟该应用相关的数据
    -- models.py  # ORM构建模型类
    -- tests.py  # 测试脚本
    -- views.py  # 视图
    
  -- manage.py  # 相当于 django-admin 代理
```

