# python笔记

[TOC]

## 数据类型和变量   
' ' or " "可表示单行字符串  
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

### 格式化

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
---|---
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

#### format()
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
#### 数字格式化
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

## list和tuple

### List
list是一种有序的集合，可以随时添加和删除其中的元素。  
len()函数可以获得list元素的个数   
用索引来访问list中每一个位置的元素，索引是从0开始的   
可以用负数表示倒数索引      

```py
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
>>> len(classmates)
3
>>> classmates[0]
'Michael'
>>> classmates[1]
'Bob'
>>> classmates[2]
'Tracy'
>>> classmates[3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
>>> classmates[-1]
'Tracy'
>>> classmates[-2]
'Bob'
>>> classmates[-3]
'Michael'
>>> classmates[-4]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

方法|用处
---|---
list.append()|往list中追加元素到末尾   
list.insert(index,data)|向index位置插入data
list.pop()|删除末尾元素
list.pop(i)|删除指定位置元素
list[i] = data| 替换索引处元素

list中数据类型可不同   
list可嵌套   
```py
>>> s = ['python', 'java', ['asp', 'php'], 'scheme']
>>>s[2][1]
'php'
```

### tuple
tuple可看作不可修改的list   
它也没有append()，insert()这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用classmates[0]，classmates[-1]，但不能赋值成另外的元素。
```py
>>> t = (1, 2)
>>> t
(1, 2)
```
“可变的”tuple：
```py
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```
## 条件判断
```py
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```
## 循环
### for...in循环
for...in循环，依次把list或tuple中的每个元素迭代出来 

所以for x in ...循环就是把每个元素代入变量x，然后执行缩进块的语句。
```py
>>>names = ['Michael', 'Bob', 'Tracy']
>>>for name in names:
...    print(name)

Michael
Bob
Tracy
```
range()函数，可以生成一个整数序列，再通过list()函数可以转换为list。比如range(5)生成的序列是从0开始小于5的整数：
```py
>>> list(range(5))
[0, 1, 2, 3, 4]

#eg: 1+.....+100
sum = 0
for x in range(101):
    sum = sum + x
print(sum)

```
### while循环

```py
#eg: 计算1-100内奇数和
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

### break & continue
```py
n = 0
while n < 10:
    n = n + 1
    if n % 2 == 0: # 如果n是偶数，执行continue语句
        continue # continue语句会直接继续下一轮循环，后续的print()语句不会执行
    print(n)
```
## dict & set
### dict
dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。
```py
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
>>> d['Adam'] = 67
>>> d['Adam']
67
```
可以用 in 判断 key是否存在
```
>>> 'Thomas' in d
False
```
二是通过dict提供的get()方法，如果key不存在，可以返回None，或者自己指定的value：
```py
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```
注意：返回None的时候Python的交互环境不显示结果。

要删除一个key，用pop(key)方法，对应的value也会从dict中删除：
```py
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```
请务必注意，dict内部存放的顺序和key放入的顺序是没有关系的。

**和list比较，dict有以下几个特点**：

查找和插入的速度极快，不会随着key的增加而变慢；
需要占用大量的内存，内存浪费多。

而list相反：

查找和插入的时间随着元素的增加而增加；
占用空间小，浪费内存很少。
所以，dict是用空间来换取时间的一种方法。

dict可以用在需要高速查找的很多地方，在Python代码中几乎无处不在，正确使用dict非常重要，需要牢记的第一条就是**dict的key必须是不可变对象**。

### set

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

要创建一个set，需要提供一个list作为输入集合：
```py
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```
注意，传入的参数[1, 2, 3]是一个list，而显示的{1, 2, 3}只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的。。

重复元素在set中自动被过滤：
```py
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```

通过set.remove(key)方法可以删除元素

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：
```py
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```
set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，同样不可以放入可变对象，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。试试把list放入set，看看是否会报错。

**再议不可变对象**   
上面我们讲了，str是不变对象，而list是可变对象。

对于可变对象，比如list，对list进行操作，list内部的内容是会变化的，比如：
```py
>>> a = ['c', 'b', 'a']
>>> a.sort()
>>> a
['a', 'b', 'c']
```
而对于不可变对象，比如str，对str进行操作呢：
```py
>>> a = 'abc'
>>> a.replace('a', 'A')
'Abc'
>>> a
'abc'
```
虽然字符串有个replace()方法，也确实变出了'Abc'，但变量a最后仍是'abc'，应该怎么理解呢？

我们先把代码改成下面这样：
```
>>> a = 'abc'
>>> b = a.replace('a', 'A')
>>> b
'Abc'
>>> a
'abc'
```
要始终牢记的是，a是变量，而'abc'才是字符串对象！有些时候，我们经常说，对象a的内容是'abc'，但其实是指，a本身是一个变量，它指向的对象的内容才是'abc'：
```
┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
```
当我们调用a.replace('a', 'A')时，实际上调用方法replace是作用在字符串对象'abc'上的，而这个方法虽然名字叫replace，但却没有改变字符串'abc'的内容。相反，replace方法创建了一个新字符串'Abc'并返回，如果我们用变量b指向该新字符串，就容易理解了，变量a仍指向原有的字符串'abc'，但变量b却指向新字符串'Abc'了：
```

┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
┌───┐                  ┌───────┐
│ b │─────────────────>│ 'Abc' │
└───┘                  └───────┘
```
所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

## 函数
函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：
```py
>>> a = abs # 变量a指向abs函数
>>> a(-1) # 所以也可以通过a调用abs函数
1
```
如果你已经把my_abs()的函数定义保存为abstest.py文件了，那么，可以在该文件的当前目录下启动Python解释器，用from abstest import my_abs来导入my_abs()函数，注意abstest是文件名（不含.py扩展名）   

pass可以作占位符   

**isinstance()**   
isinstance(object, classinfo)   
isinstance() 函数来判断一个对象是否是一个已知的类型，类似 type()。

isinstance() 与 type() 区别：   
+ type() 不会认为子类是一种父类类型，不考虑继承关系。
+ isinstance() 会认为子类是一种父类类型，考虑继承关系。   

如果要判断两个类型是否相同推荐使用 isinstance()。

eg:
```py
>>>a = 2
>>> isinstance (a,int)
True
>>> isinstance (a,str)
False
>>> isinstance (a,(str,int,list))    # 是元组中的一个返回 True
True
```
type()与isinstance()
```py
class A:
    pass
 
class B(A):
    pass
 
isinstance(A(), A)    # returns True
type(A()) == A        # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```
#### 返回多个值
```py
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny

>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0

>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```
**函数可以同时返回多个值，但其实就是一个tuple。**

定义默认参数要牢记一点：默认参数必须指向不变对象！
eg:   
```py
def add_end(L=[]):
    L.append('END')
    return L

>>> add_end()
['END']
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']

# Python函数在定义的时候，默认参数L的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。

将L指向不便对象None
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```
#### 可变参数
参数前加*

eg:
给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……
```py
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

#Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去：

>>> nums = [1, 2, 3]
>>> calc(*nums)
14
>>> calc(1,2,3)
14
```
可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。
```py
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

可以只传入必选参数，也可以传入任意个数的关键字参数。
>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}

>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}

**extra表示把extra这个dict的所有key-value用关键字参数传入到函数的**kw参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra。
```
#### 关键字参数
*后参数为关键字参数，调用时必须传入参数名
```py
def person(name, age, *, city, job):
    print(name, age, city, job)

>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer


def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)

由于命名关键字参数city具有默认值，调用时，可不传入city参数：

>>> person('Jack', 24, job='Engineer')
Jack 24 Beijing Engineer
```

#### 参数组合
在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

比如定义一个函数，包含上述若干种参数：
```py
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去。

>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}
>>> f1(1, 2, c=3)
a = 1 b = 2 c = 3 args = () kw = {}
>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
>>> f1(1, 2, 3, 'a', 'b', x=99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
>>> f2(1, 2, d=99, ext=None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}
最神奇的是通过一个tuple和dict，你也可以调用上述函数：

>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```
所以，对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。

#### 小节
Python的函数具有非常灵活的参数形态，既可以实现简单的调用，又可以传入非常复杂的参数。

默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！

要注意定义可变参数和关键字参数的语法：

*args是可变参数，args接收的是一个tuple；

**kw是关键字参数，kw接收的是一个dict。

以及调用函数时如何传入可变参数和关键字参数的语法：

可变参数既可以直接传入：func(1, 2, 3)，又可以先组装list或tuple，再通过*args传入：func(*(1, 2, 3))；

关键字参数既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过**kw传入：func( **{'a': 1, 'b': 2})。

使用*args和**kw是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。

命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。

定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数。

#### 递归

```py
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)

直接递归容易导致堆栈溢出

进行尾递归优化
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```
汉诺塔
```py
def move(n,a,b,c):
    if n == 1:
        print(a, '-->', c)
    else:
        move(n-1,a,c,b)
        move(1,a,b,c)
        move(n-1,b,a,c)
        
move(3, 'A', 'B', 'C')
```

## Py高级特性

### 切片
取出list or tuple的部分元素 

切片操作基本表达式：object[start_index:end_index:step]

step：正负数均可，其绝对值大小决定了切取数据时的‘‘步长”，而正负号决定了“切取方向”，正表示“从左往右”取值，负表示“从右往左”取值。当step省略时，默认为1，即从左往右以增量1取值。“切取方向非常重要！”“切取方向非常重要！”“切取方向非常重要！”，重要的事情说三遍！

start_index：表示起始索引（包含该索引本身）；该参数省略时，表示从对象“端点”开始取值，至于是从“起点”还是从“终点”开始，则由step参数的正负决定，step为正从“起点”开始，为负从“终点”开始。

end_index：表示终止索引（不包含该索引本身）；该参数省略时，表示一直取到数据“端点”，至于是到“起点”还是到“终点”，同样由step参数的正负决定，step为正时直到“终点”，为负时直到“起点”。

eg:
```py
a = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
切取完整对象
>>>a[:] #从左往右
>>> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

>>>a[::-1]#从右往左
>>> [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

start_index和end_index全为正（+）索引的情况
>>>a[1:6]
>>> [1, 2, 3, 4, 5]
step=1，从左往右取值，start_index=1到end_index=6同样表示从左往右取值。

>>>a[1:6:-1]
>>> []
输出为空列表，说明没取到数据。
step=-1，决定了从右往左取值，而start_index=1到end_index=6决定了从左往右取值，两者矛盾，所以为空。

连续切片操作
>>>a[:8][2:5][-1:]
>>> [4]
相当于：
a[:8]=[0, 1, 2, 3, 4, 5, 6, 7]
a[:8][2:5]= [2, 3, 4]
a[:8][2:5][-1:] = 4
理论上可无限次连续切片操作，只要上一次返回的依然是非空可切片对象。

修改单个元素
>>>a[3] = ['A','B']
[0, 1, 2, ['A', 'B'], 4, 5, 6, 7, 8, 9]

在某个位置插入元素
>>>a[3:3] = ['A','B','C']
[0, 1, 2, 'A', 'B', 'C', 3, 4, 5, 6, 7, 8, 9]
>>>a[0:0] = ['A','B']
['A', 'B', 0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

替换一部分元素
>>>a[3:6] = ['A','B']
[0, 1, 2, 'A', 'B', 6, 7, 8, 9]
```
goood eg:   (trim)
```py
def trim(s):
    if s == '' or s[0] != ' ' and s[-1] != ' ':
        return s
    if s[0] == ' ':
        return trim(s[1:])
    if s[-1] == ' ':
        return trim(s[:-1])

def trim(s):
    if s == '' or ' ' not in s[0]+s[-1]:
        return s
    if s[0] == ' ':
        return trim(s[1:])
    return trim(s[:-1])
```

### 迭代
只要是可迭代对象，无论有无下标，都可以迭代   

**DICT**   
默认情况下，dict迭代的是key。如果要迭代value，可以用for value in d.values()，如果要同时迭代key和value，可以用for k, v in d.items()。

如果要对list实现类似Java那样的下标循环怎么办？Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：
```PY
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C

上面的for循环里，同时引用了两个变量，在Python里是很常见的，比如下面的代码：

>>> for x, y in [(1, 1), (2, 4), (3, 9)]:
...     print(x, y)
...
1 1
2 4
3 9
```
### 列表生成式
```py
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

要生成[1x1, 2x2, 3x3, ..., 10x10]

>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
写列表生成式时，把要生成的元素x * x放到前面，后面跟for循环，就可以把list创建出来

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

还可以使用两层循环，可以生成全排列：
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']

```
### 生成器
generator   
循环的过程中不断推算出后续的元素

第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator。
```py
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>

如果要一个一个打印出来，可以通过next()函数获得generator的下一个返回值：

>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
9
>>> next(g)
16
>>> next(g)
25
```
for循环迭代
```py
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
... 
0
1
4
9
16
25
36
49
64
81

def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'

这就是定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。
generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

>>> for n in fib(6):
...     print(n)
...
1
1
2
3
5
8

但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中：

>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
...
g: 1
g: 1
g: 2
g: 3
g: 5
g: 8
Generator return value: done
```
### 迭代器
我们已经知道，可以直接作用于for循环的数据类型有以下几种：

一类是集合数据类型，如list、tuple、dict、set、str等；

一类是generator，包括生成器和带yield的generator function。

这些可以直接作用于for循环的对象统称为可迭代对象：Iterable。
凡是可作用于for循环的对象都是Iterable类型；

凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；

集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

Python的for循环本质上就是通过不断调用next()函数实现的，例如：
```py
for x in [1, 2, 3, 4, 5]:
    pass
实际上完全等价于：

# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```
## 函数式编程
### 高阶函数
函数本身也可以赋值给变量，即：变量可以指向函数。   
函数名其实就是指向函数的变量！也可以指向其他的对象。   
既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。  

一个最简单的高阶函数：
```py
def add(x, y, f):
    return f(x) + f(y)
当我们调用add(-5, 6, abs)时，参数x，y和f分别接收-5，6和abs，根据函数定义，我们可以推导计算过程为：

x = -5
y = 6
f = abs
f(x) + f(y) ==> abs(-5) + abs(6) ==> 11
return 11
```
#### map/reduce
map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。

f(x)=x2，要把这个函数作用在一个list [1, 2, 3, 4, 5, 6, 7, 8, 9]上，就可以用map()实现如下：
```py
def f(x):
    return x*x
print(list(map(f,[1,2,3,4,5,6,7,8,9])))

[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
reduce把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：

reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

把序列[1, 3, 5, 7, 9]变换成整数13579，reduce就可以派上用场：
```py
from functools import reduce
def fn(x,y):
    return 10*x+y
print(reduce(fn,[1,3,5,7,9]))

13579
```
str-->int
```py
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```
### filter
filter()函数用于过滤序列。

和map()类似，filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。
```py
例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
把一个序列中的空字符串删掉，可以这么写：

def not_empty(s):
    return s and s.strip()

list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))
# 结果: ['A', 'B', 'C']
```
可见用filter()这个高阶函数，关键在于正确实现一个“筛选”函数。

注意到filter()函数返回的是一个Iterator，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list。

素数筛法
```py
#定义奇数序列(生成器)
def _odd_iter():
    n=1
    while True:
        n+=2
        yield n
#过滤函数
def _not_divisible(n):
    return lambda x:x%n>0
#素数序列
def prime():
    yield 2
    it=_odd_iter()
    while True:
        n=next(it)
        yield n
        it=filter(_not_divisible(n),it)
#打印素数
for n in prime():
    if(n<1000):
        print(n)
    else:
        break
```        