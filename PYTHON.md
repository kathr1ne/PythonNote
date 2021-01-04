# PYTHON

## 环境安装

### 虚拟环境工具

[虚拟环境工具-pyenv](https://github.com/pyenv/pyenv)

[AutomaticInstaller](https://github.com/pyenv/pyenv-installer)

```Bash
# DependsInsatll
$ yum install -y git curl gcc make patch gdbm-devel openssl-devel sqlite-devel readline-devel zlib-devel bzip-devel
```

### PIP

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

### 实例化 \__new__

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

**魔术方法 一般使用要小心 弄不好就会递归**

### 可视化

```python
class A:
    def __init__(self):
        self.x = x
        self.y = y
        
	def __str__(self):  # 字符串表达 string
        return "<A {}, {}, {}>".format(self.x, self.y, self(hex(id(self)))
        
class B(list):
    pass

# print 实例化后的对象 表现不同
# 继承 mro => object.__str__
print(A(5, 7))  # <__main__.A object at 0x00000xxx> 通用方式表达  
print(B())  # []    human-readable 符号性的表达
```

**作用方法  print\str\format**

```python
t = A(5, 7)

# str\print\format 直接作用实例 调用__str__ 方法
print(t)
print(str(t))
print('{}'.format(t))

# 下面不是直接作用实例 不调用__str__ 转而调用__repr__
print((t,))
print([t])
print(str([t]))
print({t})

# result: 直接作用
<A 5, 7, addr=0x1f482195d88>
<A 5, 7, addr=0x1f482195d88>
<A 5, 7, addr=0x1f482195d88>

# result: 不是直接作用 间接作用调用__repr__
(<__main__.A object at 0x000001F482195D88>,)
[<__main__.A object at 0x000001F482195D88>]
[<__main__.A object at 0x000001F482195D88>]
{<__main__.A object at 0x000001F482195D88>}
```

```python
# print\str\format 三个函数
  - 直接作用对象上 调用 对象.__str__  => 尝试调用 __repr__ => object.__repr__ 
  - 间接作用对象上 调用 对象.__repr__ => object.__repr__
  
repr(对象) => 对象.__repr__ => ...mro... => obj.__repr__
```

