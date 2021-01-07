# PYTHON

## 环境安装

### 虚拟环境工具

[虚拟环境工具-pyenv](https://github.com/pyenv/pyenv)

[AutomaticInstaller](https://github.com/pyenv/pyenv-installer)

```Bash
# DependsInsatll
$ yum install -y git curl gcc make patch gdbm-devel openssl-devel sqlite-devel readline-devel zlib-devel bzip-devel
```



### Pyenv

#### Pyenv 目录

```bahs
~/.pyenv/
```

#### 安装其他Python版本

```bash
# 备注：如果下载太慢 直接去官网下载源码包(.tar.xz) 保存到Pyenv目录的 cache目录(需要新建)下
pyenv install <version> [-vvv]
```

#### 缓存文件
```bash
# ~/.pyenv/cahce  缓存文件 存放目录 需要手动创建
mkdir -p ~/.pyenv/cache && wget https://www.python.org/ftp/python/3.6.12/Python-3.6.12.tar.xz -P  ~/.pyenv/cache
pyenv install 3.6.12 -vvv
```

#### 更换Python版本

  - golbal 

    ```bash
    # 全局设置python版本为：3.6.12 不建议使用
    pyenv global 3.6.12  
    ```

  - shell

    ```bash
    # 当前会话：3.6.12 仅对当前会话(shell|session|terminal)有影响   
    pyenv shell 3.6.12  
    ```

  - local  

    ```bash
    # 把目录当成项目 每个项目可以使用不同版本
    # 将Pyehon version绑定目录 子目录同样有效 离开目录失效
    pyenv local 3.6.12
        
    # 这样使用 虽然每个项目(不同目录) 使用了不同版本 但是如果2个项目使用同一个版本 
    # 一个需要装django 1.8 另一个需要django 2.2 就会产生冲突
        
    # 解决方案：使用虚拟环境
    ```

    

#### 虚拟环境

```bash
pyenv virtualenv 3.6.12 virtualenv-3612  # 根据需要的版本 创建一个虚拟环境
cd $project_dir
pyenv local virtualenv-3612  # 为当前项目 设置虚拟环境
```



### PIP配置

[Aliyun源](http://mirrors.aliyun.com/)

```Bash
# Windows配置文件
~/pip/pip.ini

# Linux配置文件
~/.pip/pip.conf

# 内容
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

## 基础语法

### 字符串

```Bash
# 裸字符 r'str' r前缀
r'\n\tabcd'

# f字符串 插值 格式化字符串

# 续行 \

# 缩进

```

### 语言区别

```bash
# 动静
变量定义 是否需要事先申明类型

# 强弱 强制类型转换
自动转换 不报错     ---- 弱类型
不能做隐式类型转换   ---- 强类型
```

### 运算符

```python
# 除法 python2 都是整除
1/2     # 自然除法 python2 3除法不一样 差异
1//2    # 整除 向下取整

# 取模 取余
5 % 2   4 % 2

# 位运算符
& | ^ << >>
~ 按位取反 包括符号位

# 判断奇数
7 % 2 == 1    # 取余 判断结果是否为1
7 & 1 == 0    # 与运算 位与:按位相乘

0111
0001 &
-------
0001  == 1 

# 移位： <<   >>
<< 左移 == *2 *2 *2 *2 ...
>> 右移 == /2 /2 /2 /2 ...

0111    # 7
1110    # 14 = 7 * 2 | 7 << 1
11100   # 7 << 2 左移2位

# 短路运算
运算符 优化 -- 尽量短路 
- 逻辑运算符返回值不一定是Bool型
- 最频繁使用 决定整个逻辑表达式的值 尽量往前放

```

## 面向对象进阶

[魔术方法参考](https://pyzh.readthedocs.io/en/latest/python-magic-methods-guide.html)

**魔术方法 一般使用要小心 弄不好就会递归**

###  \__new__

```python
# __new__ 作用
# 1. 用在元编程当中 type构造类对象有关 metaclass
# 2. 构造实例

class A:
    def __new__(cls, *args, **kwargs):  # 实例化第一阶段：实例化
        print(cls)     # A类型
        print(args)    # 收集位置传参为元组
        print(kwargs)  # 收集关键字传参为字典
        # return None/return 'abc'
        # return cls(*args, **kwargs) RecursionError 递归异常
        # return object.__new__(cls)
    	return super().__new___(cls)
        # 实例化不能自己做 交给根基类实现 调根基类的__new__方法 
        # 传参cls 根据类型 制造实例
        # 没有绑定效果 永远是 静态方法
    
    def __init__(self):  # 实例化第二阶段：初始化
		pass

t = A()  

# 1. 实例化 => __new__(cls) 制造实例
# 2. 初始化 => __init__(self)

# 实例化伪代码
1. obj =  object.__new__(A)  # obj = instance
2. obj.__init__()  # 该方法不能有返回值 隐式 return None
3. return obj
```

```python
class A:
    def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)
        instance.z = 200  # 建议放到__init__ 初始化方法
        cls.Z = 300  # 直接放到类属性定义 否则每次实例化都会重复覆盖Z属性
        return instance
   
	def __init__(self):
        pass
```

### 可视化

#### \__str__ 

实例的字符串表达

```python
class A:
   def __init__(self):
        self.x = x
        self.y = y
        
    # 该实例的字符串表达 string
  	def __str__(self):  
        return "<A {}, {}, {}>".format(self.x, self.y, self(hex(id(self)))
```

#### \__repr__ 

实例的非字符串表达

  ```python
class A:
    # 该实例的 非字符串表达形式
    def __repr__(self):
         return 'repr '  
                                         
    __str__ = __repr__  # 常用
          
class B(list):
    pass
  
# print 实例化后的对象 表现不同
# 继承 mro => object.__str__ 基类定义一个即可
print(A(5, 7))  # <__main__.A object at 0x00000xxx> 通用方式表达  
print(B())  # []    human-readable 符号性的表达
  ```

  **作用方法影响**

 print\str\format

  ```python
t = A(5, 7)
  
# str\print\format 直接作用实例 调用__str__ 方法
print(t)
print(str(t))
print('{}'.format(t))
  
# 下面不是直接作用实例 不调用__str__ 转而调用__repr__
print((t,))  # 作用在元组上
print([t])   # 作用在列表上
print(str([t]))
print({t})
  
# result: 直接作用
>>> <A 5, 7, addr=0x1f482195d88>
>>> <A 5, 7, addr=0x1f482195d88>
>>> <A 5, 7, addr=0x1f482195d88>
  
# result: 不是直接作用 间接作用调用__repr__
>>> (<__main__.A object at 0x000001F482195D88>,)
>>> [<__main__.A object at 0x000001F482195D88>]
>>> [<__main__.A object at 0x000001F482195D88>]
>>> {<__main__.A object at 0x000001F482195D88>}
  ```

  ```python
# print\str\format 三个函数
  - 直接作用对象上 调用 对象.__str__  => 尝试调用 __repr__ => object.__repr__  # 先继承(除object) 没有调用self.__repr__
  - 间接作用对象上 调用 对象.__repr__ => object.__repr__
    
repr(对象) => 对象.__repr__ => ...mro... => obj.__repr__
  ```

**Path类 例子**

```python
from pathlib import Path

p1 = Path('/etc/sysconfig/network')
print(p1)    # print直接作用到p1上 __str__
print([p1])  # print作用到列表上   __repr__

# open 拿到的是个Path对象 不是字符串
# 小心 可能会报错 这里不报错时因为：pathlib是Python内建函数 很多都已经能够使用Path实例 (内部会做判断)
with open(p1) as f: 
    pass

# 第三方函数(文件对象 or 路径字符串) -- OK
# 第三方函数(str(p1))  p1.__str__  返回路径字符串 -- OK
# 第三方函数(p1) == 第三方函数(repr(p1))  类型不对 -- Not OK

# 下面结果说明 Path类的 __str__ 和 __repr__ 不一样
>>> '\etc\sysconfig\network'
>>> [WindowsPath('/etc/sysconfig/network')]
```

#### \__bytes__

```python
print(bytes(t))  # 报错 没有object兜底
>>> TypeError: cannot convert 'A' object to bytes

class A:
    # 该实例的 非字符串表达形式
    def __repr__(self):
        return 'repr '

    __str__ = __repr__  # 常用

    def __bytes__(self):  # bytes(t)
        return str(self).encode()  
        # str(self) => str(t) => t.__str__

t = A()
print(bytes(t))
>>> b'repr '
```

| 方法       | 意义                                                         |
| ---------- | ------------------------------------------------------------ |
| \__str__   | str()\format()\print()函数调用 需要返回对象的字符串表达 <br>如果没有定义 就去调用\__repr__方法返回字符串表达 <br>如果\__repr__没有定义 就直接返回对象的内存地址信息 |
| \__repr__  | 内建函数repr()对一个对象获取**字符串**表达 <br>调用\__repr__方法返回字符串表达 如果 \__repr__也没有定义 就直接返回object的定义(显示内存地址信息) |
| \__bytes__ | butes()函数调用 返回一个对象的bytes表达 即返回bytes对象      |

### \__bool__

```python
class A:
    pass

if A:  # 类对象 默认为真 => if bool(A)
    print('real A')

if A():  # 类实例 默认为真 => if bool(A())
    print('read A()')
    
>>> real A
>>> read A()

class B:
    def __bool__(self):  # self 跟当前实例(B的实例)有关
        return False
        # return bool(self)  递归

print(bool(B))  # B 类对象 type的实例
>>> True  # 类对象 默认为真

t = B()
print(bool(t))
>>> False # t.__bool__()
```

### \__len__

```python
# 空容器 等效False 仅Python
  - []
  - ()
  - {}
  - set()
  - ''
  - b''
# 
  - None
  - 0

class B:
    def __len__(self):
        print('enter __len__')
        # return 100 -> True
        # 返回值 >=0; 0 False; >0 非空True
        # True if > 0 else False
        return 0
    
t = B()
print(bool(t))  # bool(t) => t.__bool__ => t.__len__
>>> enter __len__
>>> False

# 类实例 没有__bool__ __len__ 默认为真
# 类对象 默认为真 是type实例
class B:
    pass

bool(B), bool(B())
>>> (True, True)
```

| 方法         | 意义                                                         |
| ------------ | ------------------------------------------------------------ |
| \_\_bool\_\_ | 内建函数bool() 或者对象放在**逻辑表达式**的位置 调用这个函数 返回布尔值<br>没有定义\__bool__() 就找\__len_()返回长度(>=0) 非0为真<br>如果\__len__()也没有定义 那么所有实例都返回真 |

### \__slots__

```python
# 用tuple定义允许绑定的属性名称
# 限制实例的属性
# 使用__slots__要注意 __slots__定义的属性仅对当前类实例起作用 对继承的子类是不起作用的
class Student:
    __slots__ = ('name', 'age') 

```



