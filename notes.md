# python笔记

[toc]

## 数据类型和变量   
' '可表示单行字符串  
''' '''可表示多行字符串  
前面加上r 字符串中的转义字符默认不转义  

python中任何数据都看成一个“对象”   
Python的浮点数也没有大小限制，但是超出一定范围就直接表示为inf（无限大）。

## 字符串和编码
### 字符编码
字符串是以Unicode编码的。   
对于单个字符的编码，Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符。   

### 字符串

Python对bytes类型的数据用带b前缀的单引号或双引号表示：
```python
x = b'ABC'
```
要注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节。

以Unicode表示的str通过encode()方法可以编码为指定的bytes，例如：
```python
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
```
纯英文的str可以用ASCII编码为bytes，内容是一样的，含有中文的str可以用UTF-8编码为bytes。含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错。

**在bytes中，无法显示为ASCII字符的字节，用\x##显示。**

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法：
```python
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```
如果bytes中包含无法解码的字节，decode()方法会报错。如果bytes中只有一小部分无效的字节，可以传入errors='ignore'忽略错误的字节。
```python
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```
要计算str包含多少个字符，可以用len()函数。
如果换成bytes，len()函数就计算字节数。
```python
>>> len('ABC')
3
>>> len('中文')
2
>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6
```
**1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节。**

在操作字符串时，我们经常遇到str和bytes的互相转换。为了避免乱码问题，应当始终坚持使用UTF-8编码对str和bytes进行转换。

### 代码前注释
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```
第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

## 格式化

在Python中，采用的格式化方式和C语言是一致的，用%实现，举例如下：
```python
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```
%运算符就是用来格式化字符串的。在字符串内部，%s表示用字符串替换，%d表示用整数替换，有几个%?占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个%?，括号可以省略。

常见的占位符有：

占位符|	替换内容
---|:--:|---
%d|整数
%f|浮点数
%s|字符串
%x|十六进制整数

eg:
```python
>>> print('%2d-%02d' % (3, 1))
 3-01
>>> print('%.2f' % 3.1415926)
3.14
```
如果你不太确定应该用什么，%s永远起作用，它会把任何数据类型转换为字符串：
```python
>>> 'Age: %s. Gender: %s' % (25, True)
'Age: 25. Gender: True'
```
有些时候，字符串里面的%是一个普通字符怎么办？这个时候就需要转义，用%%来表示一个%：
```python
>>> 'growth rate: %d %%' % 7
'growth rate: 7 %'
```

### format()
另一种格式化字符串的方法是使用字符串的format()方法，它会用传入的参数依次替换字符串内的占位符{0}、{1}……，不过这种方式写起来比%要麻烦得多：
```python
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```

基本语法是通过 {} 和 : 来代替以前的 % 。

format 函数可以接受不限个参数，位置可以不按顺序。

实例
```python
>>>"{} {}".format("hello", "world")    # 不设置指定位置，按默认顺序
'hello world'
 
>>> "{0} {1}".format("hello", "world")  # 设置指定位置
'hello world'
 
>>> "{1} {0} {1}".format("hello", "world")  # 设置指定位置
'world hello world'
```
也可以设置参数：

实例
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
print("网站名：{name}, 地址 {url}".format(name="菜鸟教程", url="www.runoob.com"))
 
# 通过字典设置参数
site = {"name": "菜鸟教程", "url": "www.runoob.com"}
print("网站名：{name}, 地址 {url}".format(**site))
 
# 通过列表索引设置参数
my_list = ['菜鸟教程', 'www.runoob.com']
print("网站名：{0[0]}, 地址 {0[1]}".format(my_list))  # "0" 是必须的
```
输出结果为：

网站名：菜鸟教程, 地址 www.runoob.com

网站名：菜鸟教程, 地址 www.runoob.com

网站名：菜鸟教程, 地址 www.runoob.com

*也可以向 str.format() 传入对象：*

实例
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class AssignValue(object):
    def __init__(self, value):
        self.value = value
my_value = AssignValue(6)
print('value 为: {0.value}'.format(my_value))  # "0" 是可选的
```
输出结果为：value 为: 6
### 数字格式化
下表展示了 str.format() 格式化数字的多种方法：
```py
>>> print("{:.2f}".format(3.1415926));
3.14
```
数字|	格式|	输出|	描述
---|:-----:|:-----:|:-----|
3.1415926|	{:.2f}|	3.14|	保留小数点后两位
3.1415926|	{:+.2f}|	+3.14|	带符号保留小数点后两位
-1|	{:+.2f}|	-1.00|	带符号保留小数点后两位
2.71828|	{:.0f}|	3|	不带小数
5|	{:0>2d}|	05|	数字补零 (填充左边, 宽度为2)
5|	{:x<4d}|	5xxx|	数字补x (填充右边, 宽度为4)
10|	{:x<4d}|	10xx|	数字补x (填充右边, 宽度为4)
1000000|	{:,}	|1,000,000|	以逗号分隔的数字格式
0.25|	{:.2%}	|25.00%	|百分比格式
1000000000|	{:.2e}|	1.00e+09	|指数记法
13|	{:10d}	        |13	|右对齐 (默认, 宽度为10)
13|	{:<10d}|	13|	左对齐 (宽度为10)
13|	{:^10d}|	    13|	中间对齐 (宽度为10)
11	|'{:b}'.format(11)  '{:d}'.format(11)  '{:o}'.format(11)  '{:x}'.format(11)  '{:#x}'.format(11)  '{:#X}'.format(11)|	1011  11  13   b  0xb  0XB	|进制

^, <, > 分别是居中、左对齐、右对齐，后面带宽度， : 号后面带填充的字符，只能是一个字符，不指定则默认是用空格填充。

表示在正数前显示 +，负数前显示 -；  （空格）表示在正数前加空格

b、d、o、x 分别是二进制、十进制、八进制、十六进制。