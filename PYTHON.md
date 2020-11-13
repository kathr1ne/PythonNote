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

