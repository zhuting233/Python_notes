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
