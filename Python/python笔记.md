# 前言

此笔记是基于python官网文档整理出来的，版本号为python3.8.x  (主版本号.此版本号.修订号)

- 主版本号：做了不兼容的 API 修改.
- 次版本号：做了向下兼容的功能性新增.
- 修订号：做了向下兼容的问题修正.

python是动态类型/解释性语言，默认靠Cpython解释器实现.

python解释器执行流程：`(.py)源文件` ==> `(Cpython)字节码` ==> `机器码`

# 基础

- 语言角度：词法+语法+语义
  - 词法 `lexical`：https://docs.python.org/zh-cn/3.8/reference/lexical_analysis.html#lexical-analysis
    - 行结构
    - 形符：Python程序由一个<u>解析器</u>读取. 输入到解析器的是一个由<u>词法分析器</u>所生成的<u>形符</u>流.
      - NEWLINE, INDENT, DEDENT
      - 标识符 = 关键字 + 保留的标识符
      - 字面值：用于表示一些内置类型的常量.
      - 运算符
      - 分隔符
  - 语法 `syntax`：https://docs.python.org/zh-cn/3.8/reference/expressions.html#expressions
    - 表达式
      - 原子：表达式的最基本构成元素.
        - 名称：作为原子出现的标识符.
        - 列表、集合和字典的推导式
      - 原型：原型代表编程语言中最紧密绑定的操作.
        - 属性引用
        - 抽取
        - 切片
    - 简单语句、符合语句
  - 语义：`context`上下文
- 实现角度：数据模型+执行模型
- (语言参考) https://docs.python.org/zh-cn/3.8/reference/index.html

## 数据模型

(语言参考-数据模型)https://docs.python.org/zh-cn/3/reference/datamodel.html

### 内存模型

- 对象 `object`：python程序中的所有数据都是由<u>对象或对象间关系</u>来表示的.  每个对象都有各自的<u>编号、类型和值.</u> 
  - 对象的编号在对象创建后便不再改变. 编号可理解为对象在内存中的地址.
  - 对象的类型决定该对象所支持的操作并定义了该类型的对象可能的取值.
  - 值可以改变的对象被称为可变的`mutable`；值不可以改变的对象就被称为不可变的`immutable`.
    - 可变对象的内容可变，变量引用没变(id没变). `list, dict, bytearray, set`
    - 不可变对象的内容不可变，修改值本质上是让变量指向一个含有新值的新对象(id变化). `int, boolean, float, string, tuple, bytes, frozenset`
  - 若对象里包含了对其他对象的引用，此类对象被称为容器`container`. 
- 变量 `variable`：**变量是对象(内存空间)的引用.**

  - python中变量用下划线命名法. 由于python不区分大小写，驼峰命名法不适用.
  - 在python中，通常用全部大写的变量名表示常量. 实际上还是变量，这只是一个习惯上的用法.
- 垃圾回收机制 `GC`

  - python中，一切变量都是对于内存空间的引用，会给每个空间标上一个id。

```python
# 容器、可变、不可变
## eg1
a = ([1],2)
b = a
id(a)
id(b)
a is b	# True
a[0] = [1,2,3]	# TypeError a[0]作为引用已变为[1,2,3]的空间地址
a[0].append(2)	# a[0]作为引用并未改变，改变的是引用的空间内存的值
print(a)	# ([1,3],2)
print(b)	# ([1,3],1002
id(a)
id(b)
a is b	# True

a = 'wzy'
b = a
a is b	# True
a = 'frank'
id(a)	# 变化！
id(b)
a is b	# False

a = [1,2,3]
b = a
a is b	# True
a = [1,2,3]
id(a)	# 变化！
id(b)
a is b	# False

## eg2
a = [1,2,3]
b = a
print(b)	# [1,2,3]
id(a)
id(b)
a is b	# True

a = [1,[2],3]
print(b)	# [1,2,3]
id(a)	# 变化！新的空间的地址赋予了a
id(b)
a is b	# False

b = a
print(b)	# [1,[2],3]
id(a)
id(b)	# 变化！a指向的空间的地址赋予了b
a is b	# True
a[1].append(4)	# [1,[2,4],3]
print(b)	# [1,[2,4],3]
id(a)
id(b)
a is b	# True

a[1] = [0]
print(a)	# [1,[0],3]
print(b)	# [1,[0],3]
id(a)	# id(a[1])变了！
id(b)
a is b	# True

a[1] = (0,)
print(a)	# [1,(0,),3]
print(b)	# [1,(0,),3]
id(a)	# id(a[1])变了！
id(b)
a is b	# True

a[1] = [0]
print(a)	# [1,[0],3]
print(b)	# [1,[0],3]
id(a)	# id(a[1])变了！
id(b)
a is b	# True
```

### 数据类型

- 空值 `None`
- 数字 `number`
  - 整型 `int`
  - 布尔型 `bool`
  - (双精度)浮点数 `float`
  - 复数 `complex`
- 序列 `sequence`
  - 不可变序列
    - 字符串 `string`
    - 元组 `tuple`
    - 字节串 
  - 可变序列
    - 列表 `list`
    - 字节数组
- 集合
  - 集合 `set`
  - 冻结集合
- 映射 `mapping`
  - 字典 `dict`
- 可调用类型 `callable`
  - 用户定义函数
  - 实例方法
  - 生成器函数、协程函数、异步协程函数
  - 内置函数、内置方法
  - 类、类实例
- 模块 `module`
- 自定义类
- 类实例
- I/O对象(文件对象)
- 内部类型

#### 空值

- python里独有的特殊值，用`None`表示.
- None不能理解为0，0是有意义的，None是一个特殊的值.
- None代表的逻辑值是`False`.

#### 整型

#### 布尔型

- `True`、`Flase`，注意大小写！其分别和整型数1、0对应。
- (标准库参考-内置类型)https://docs.python.org/3/library/stdtypes.html#truth-value-testing

#### 浮点型

- 整数与浮点数都是immutable.

- 从机器层看实际是双精度浮点数。
- 科学计数法：`1.2e-5`意为1.2*(10**(-5))
- 整数永远是精确的，浮点数可能会有四舍五入的误差。具体原因参见《计算机组成原理》。
- python中，整数和浮点数都没有大小限制，但超出范围就直接表示为`inf`(无限大)。

#### 字符串

- 字符串是immutable. 
- `'I\'m \'OK\'!'` 内外使用相同的(单/双)引号，需要转义`\`.
- `'''...'''` 可表示多行内容.
- `r' '`表示`' '`内部的字符串默认不转义.
- `b' '`表示`' '`内部的字符串以字节为单位，每个字符只占1B.
- **编码问题** `encode()`、`decode()`
  - `ASCII`是1B，`Unicode`通常是2B.
  - `UTF-8`把一个`Unicode`字符根据不同的数字大小编成1-6B，节约空间，提高传输效率。
  - 一个中文字符经过utf-8编码后，通常占用3B。
- **字符串格式化**
  - `'...%s...%d' % ... `  类似C语言。
    - `%d` 整数
    - `%f` 浮点数
    - `%s` 字符串
    - `%x` 十六进制整数
  - `str.format()` python3.6-的推荐用法。
  - `f'...{x}...'` Python3.6+引入，类似字符串模板。
  - `from string import Template` python标准库。适用于处理用户的输入，保证安全性。

#### 列表

- 列表属于mutable.
- python内置的有序集合，可随时增加、删除元素，元素类型可以不同.
- 所有分片操作都会返回新列表，属于深拷贝.
- 查找和插入随着元素的增加而增加.
- 占用空间小，内存浪费少.
- 列表只支持  `+` 操作符运算，注意与`numpy`中的矢量运算区分.

#### 元组

- 元组属于immutable，本质上是元素的指向不变.

- python内置的有序集合，一旦初始化就不能修改元素，所以代码安全.

#### 字典

- **key必须是immutable。**
- 在其他语言中被称为map(即映射)，使用key-value存储，查找速度极快。
- 一个key只能对应一个value，多次对key赋值，后一次赋值会覆盖前一次赋值。
- 可通过 `in` 或 `get()` 判断key是否存在，避免key不存在而报错。
- 查找和插入操作速度极快，不会受到key的数量影响。
- 需要占用大量内存，内存浪费多。

#### 集合

- python中，集合是由不重复且不可变对象组成的无序且有限的集合。

```python
# 字符串 string
#!/usr/bin/env python3		告诉Linux系统，这是一个python可执行程序
# -*- coding: utf-8 -*-		告诉python解释器，按照utf-8源码读取源代码，同时还必须保证编辑器使用UTF-8 without BOM
print('\u4e2d\u6587') 			# '中文'
'ABC'.encode('ascii') 			# b'ABC'
x = '中文'.encode('utf-8') # b'\xe4\xb8\xad\xe6\x96\x87'
len('中文')		 						 # 2
len(x) 										 # 6

---
from string import Template
name = '小明'
percent = 17.125
'Hello, {0}, 成绩提升了 {1:.1f}%'.format(name, percent)
'Hello, {name}, 成绩提升了 {percent:.1f}%'.format(name=name, percent=percent)
'Hello, %s, 成绩提升了 %.1f%%' % (name, percent) # %%触发转义，转义后为%
f'Hello, {name}, 成绩提升了 {percent}%'
template_str = 'Hello, $name, 成绩提升了 $percent%'
print(Template(template_str).substitute(name=name, percent=percent))

# 元组 tuple
tuple1 = (1) 	# 初始化数字1
tuple2 = (1,) # 初始化元组
tuple3 = () 	# 元组

# 列表 list
l1 = [1,2,3]
l2 = [1,2,3]
l1 + l2 # [1,2,3,1,2,3]
l1 * l2	# TypeError

# 字典 dic
dict1 = {'wzy':24, 'frank':18}

# 集合 set
s = set([1,2,3,3]) # {1,2,3}

# 切片 slice
## list, tuple都支持切片，切片会返回一个新的对象
list = [1,2,3,4,5,6,7,8,9]
print(list[0:3]) 	 #  [1,2,3]	取索引处于[0,3)内的元素
print(list[:3])	 	 #	[1,2,3]	0可省略
print(list[-3:])	 #	[7,8,9]
print(list[:])		 # 	[1,2,3,4,5,6,7,8,9]
print(list[:8:2])	 # 	[1,3,5,7]	索引[0,7)内，每2个元素取一个
```

## 执行模型

https://docs.python.org/zh-cn/3/reference/executionmodel.html

- 程序结构
  - python程序是由代码块构成的. 
  - `代码块` 是被作为一个单元来执行的一段python程序文本.
    - 模块
    - 函数体
    - 类定义
    - 交互式输入的每条命令
  - 代码块在执行帧中被执行. 一个帧会包含某些管理信息（用于调试）并决定代码块执行完成后应前往何处以及如何继续执行.
- 命名与绑定
  - `名称` 用于指代对象.  名称是通过名称绑定操作来引入的.
  - `作用域` 定义了一个代码块中名称的可见性.
- 异常

### 异常

- 错误
- 异常
  - 处理异常
  - 抛出异常
  - 自定义异常
  - 定义清理操作/预定义的清理操作

```python
# 关键字: try catch except finally assert raise
try:
  # 用于监听可能出错的代码段，错误发生处后面的代码不再执行。
  assert	n != 0, 'n is zero'	# 断言
  raise ValueError('invalid value')	# 手动抛出错误，错误类型支持自定义错误类型
except Exception as e:
  logging.exception(e)
  # 错误发生时才会执行。
  # 错误也是类型，所有错误类型继承自BaseException，父类错误会把该类错误以及子类错误全部捕捉到。
finally:
  # try或except执行完后，最后执行
```



# 面向过程

## 函数

- 函数定义
  - `return`
    - 没有写return语句，函数执行完毕后也会返回结果`None`.
    - `return None`, `return`, `不写明return语句` 同等效用.
    - 可返回多个值，本质上是返回一个tuple.
  - 参数
    - 位置参数
    - 默认参数
      - 必选参数在前，默认参数在后.
      - 不按默认参数顺序传值时，需要在传值时写上参数名.
      - **默认参数最好指向`immutable`，否则结果可能难以预料**.
    - 可变参数 `*args`：允许传入任意(包括0)个参数，并将其自动组装为一个tuple.
    - 关键字参数 `**kw`：允许传入任意(包括0)个参数，并将关键字参数自动组装为一个dict.
    - 命名关键字参数：允许限制关键字参数的名字.
    - **参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数、关键字参数.**
  -  `pass`：空函数.
  - `yield`：函数中含有yield，则该调用函数时返回一个`generator`.
- 函数调用 
  - 内置函数(标准库参考-内置函数) https://docs.python.org/3/library/functions.html

```python
# 函数定义
def func(a, b, c=3, d=4):
	return a, b, c, d

func(1,2) 		# 1,2,3,4
func(1,2,5)		# 1,2,5,4
func(1,2,d=5) # 1,2,3,5

# 默认参数
def func1(list=[]):
  	list.append('err')
  	return list
func1() # ['err']
func1() # ['err', 'err']	默认参数是可变对象，两次调用使用的默认参数是同一个对象。

# 可变参数
def func1(list):
	return list
      
def func2(*list): # list是可变参数
  return list

list = [1,2,3]
func1(list) 	# [1,2,3]
func1(1,2,3) 		# TypeError
func2(1,2,3) 		# (1,2,3) 函数接收到的是一个tuple
func2() 				# ()
func2(*list)		# (1,2,3)

# 关键字参数
def func1(a, b, **c):	# c是关键字参数
  	print(a, b, c)

def func2(a, b, *, c, d): # 命名关键字参数
  print(a,b,c,d)

def func3(a, b, *c, d=10, e): # 命名关键字参数
  print(a,b,c,d,e)  
  
func1(1, 2)					# 1 2 {}
func1(1,2,c=3,d=4)	# 1 2 {'c':3, 'd':4}
func1(1,2,d=3,e=4)	# 1 2 {'d':3, 'e':4}	可随意命名参数

func2(1,2,d=3,e=4)	# error
func2(1,2,c=3,d=4)	# 1 2 {'c':3, 'd':4}	必须与*后的参数名字对应，位置可不对应

func3(1,2,d=3,e=5)	# 1 2 () 3 5
func3(1,2,10,d=3,e=5)	# 1 2 (10,) 3 5
func3(1,2,10,11,e=3,d=5)	# 1 2 (10,11) 5 3
func3(1,2,10,11,e=3)			# 1 2 (10, 11) 10 3

def func4(a, b, c=0, *args, **kw):
	print(a,b,c,args,kv)
  
args1 = (1,2)
args2 = (1,2,3,4)
kw = {'name':'frank', 'age':24}
func4(*args1, **kw) # 1 2 0 () {'name': 'frank', 'age': 24}		tuple用*序列化，dict用**序列化
func4(*args2, **kw) # 1 2 3 (4,) {'name': 'frank', 'age': 24}
func4(args1,kw) # (1, 2) {'name': 'frank', 'age': 24} 0 () {} 
```

## 生成器

- 可迭代对象 `iterable` 
  - 可迭代对象  <==> 可直接作用于for循环
  - 对象内部实现`__iter__()`方法或`__getitem__()`方法，调用该方法返回一个迭代器. 
  - list, tuple, string, dict只是iterable对象. 本质上，for循环时默认调用对象的`__iter__()`方法生成一个迭代器，再不断调用迭代器的`__next__()`方法.
- 迭代器 `iterator`
  - 迭代器 <==> 可迭代对象+可被`next()`调用
  - iterable对象内部实现`__next__()`方法. 对象被next()函数调用时，会执行该方法，返回迭代中的下一项或引起`StopIteration`异常. 
  - `iter()` 可将iterable对象转化为iterator. 直接转化成迭代器需要将原料一次性加载到内存.
  - 可用`isinstance()`判断对象是iterable对象还是iterator.
  - **迭代器的遍历具有一次性，遍历完一次便不可再遍历**.

- 生成器 `generator`：一种特殊的迭代器，又称为生成迭代器 `generator iterator`. 

  - 生成器函数 `generator function`：含有`yield`关键字的函数.

    - **生成器不会像列表一样把所有元素一次性加载到内存，而是延迟计算的方式返回元素**. 适用于生成迭代器进行海量数据的遍历. 
    - 生成器函数的返回值是一个生成器，yield对应的值在调用`next()`时返回. 生成器迭代终结时，也会自动抛出异常`StopIteration`.
- 每个yield会临时暂停处理，记住当前位置执行状态(包括局部变量和挂起的 try 语句).
    - 生成器支持的方法(语言参考-表达式-yield表达式)：https://docs.python.org/zh-cn/3/reference/expressions.html#generator-methods
- 生成器表达式 `generator expression`：类似列表推导式.
  

```python
# 迭代器
dict = {'a':1, 'b':2, 'c':3}
for key in dict:
  print(key)	#	a b c	dict默认遍历key
  
for val in dict.values():
  print(val)	# 1 2 3
  
for k,v in dict.items():
  print(k,v)	# a 1 b 2 c 3

for index,val in ['a','b','c']:	# 索引-元素对
  	print(index,val)	# 0 a 1 b 2 c
    
for x, y in [(1, 1), (2, 4), (3, 9)]:	# 同时引用多个变量
  print(x,y)	# (1,1) (2,4) (3,9)
iter([]) # 把Iterable变成Iterator

# 生成器
l = [x if x % 2 == 0 else -x for x in range(1, 11)]	# 列表推导式
g = (x if x % 2 == 0 else -x for x in range(1, 11))	# 生成器表达式	g是生成器，不是元组！
next(g) # -1
next(g)	# 2
...
next(g) # 10
next(g)	# StopIteration

def func(n):	# 生成器函数
  for i in range(n):
    print i
    yield i
  print('done')
  
g = func(3)
next(g)	# 0
next(g)	# 1
next(g)	# 2
next(g)	# StopIteration: done		返回值包含在异常的value中，遍历生成器常与try..catch组合使用

str = '123'
def func(str):
	for c in str:
		yield int(c)

g = func(str)
all = sum(g)
print(all) 		# 6
for c in g:
	print(c)		# 无输出，因为迭代器g已经遍历完，迭代器的遍历具有一次性
```

## 函数式编程

函数式编程：python部分支持函数式编程，而非纯函数式语言.

- 高阶函数：其参数是一个函数.
- 返回函数：其返回值是一个函数. 引出闭包`closure`概念！
- 匿名函数：`lambda`不需要显式定义函数.
- 装饰器 `decorator`：一种在代码运行期间动态增加功能的方式，本质上就是一个返回函数的高阶函数.
- 偏函数 ：可使用`functools.patial`创建一个新函数，简化了函数调用的传参.

```python
# 高阶函数
def func(x):
  return 2*x

m = map(func, [1,2,3])
print(m)				# <map object at ...>
print(next(m))	# 2
print(list(m))	# [4,6]	
```

### 匿名函数

```python
# 匿名函数
func = lambda x: x*x
func(2)	# 4
map(lambda x:x*x, [1,2,3])
```

### 装饰器

```python
# 装饰器
def log(f):	# decorator
  def wrapper(*args, **kw):
    print(f"log: call {f.__name__}")
    return f(*args, **kw)
  return wrapper

@log
def func():
  print('yes')
  
func() 						# log: call func\n	yes
func = log(func)	# 没有装饰器的用法
func()

def log(info):
  def decorator(f):
    def wrapper(*args, **kw):
      print(f"log: call {f.__name__} {info}")
    	return f(*args, **kw)
    return wrapper
  return decorator

@log('test')
def func():
  print('yes')
  
func() # log: call func test\n	yes
func = log('test')(func)	# 没有装饰器的用法
func()
```



# 面向对象

面向对象的三大特点：数据封装、继承和多态.

面向对象的两个重要概念：类`class`和实例`instance`.

## 类

- 类与对象
  - **类是对象的模板，对象是类的实例。**很明显，python属于基于类的面向对象语言。
  - 定义一个class时，就等同于定义了一种新的数据类型，故可用`isinstance()`检测实例。
  - 创建对象的步骤：1、在内存中为对象分配空间；2、调用`__init__`方法。
  - python是动态语言，其允许对实例绑定任何变量和方法，其他实例并不共享的。
- 方法
  - `__init__(self, ...)`：构造函数。
    - 第一个参数永远是`self`。
    - 有了`__init__`方法，创建实例的时候就不能再传空参数，应与之一一对应匹配(self不用传)。
  - 内建属性操作方法：`setattr()`、`getattr()`、`hasattr()`
  - 类方法：类所有，所有实例共享，可动态CRUD。
  - `__iter__()`：要想对象支持for循环，必须实现生成可迭代对象的方法。
  - `__next__()`：for循环不断调用对象的next方法，拿到循环的下一个值，直到StopIteration退出循环。
  - `__getitem__()`：使对象支持索引操作符。
  - `__getattr__()`
  - `__call__()`：实例支持函数方式调用，设置了该方法的对象称为`Callable`类型。模糊了对象与函数的界限。
  - 特殊方法
- 属性
  - 实例属性：每个实例独有，互不干扰。
  - 类属性：类所有，所有实例共享，可动态CRUD。
  - 私有属性：`__xxx`。只有对象内部能访问，外部无法访问。
  - `__slots__`：限制实例的属性。子类实例允许定义的属性就是自身的`__slots__`加上父类的`__slots__`
## 继承与多态
- 继承：类与类间的关系，子类通过继承获得父类的全部功能。
  - 多态：当子类方法与父类重名时，子类方法会覆盖父类方法，且在运行时总会调用子类方法。
  - 多重继承：子类继承自多个父类，也被称为`MixIn`设计模式。Java是单继承，故不支持MixIn。
## OOP
  - `@property`
  - `__xxx__`都是具有特殊用途的属性和方法。
  - 枚举类：`Enum`类型。
  - `type()`：1、动态创建类；2、查看类型。
  - 元类`metaclass`：元类是类的模板，类是元类的实例。元类可以改变类创建时的行为。

```python
class Animal(object):	# 类名一般约定为大写开头
  	__slots__ = ('name', '__age')	# 严格匹配允许设置的属性名
  	def __init__(self,name,age):	# 构造函数
      self.name = name
      self.__age = age	# 私有属性
      
    def getAge(self):
      	print(self.__age)
        
    def run(self):
      	print('animal is running...')

class Dog(Animal):	# Dog类继承自Animal类
  	count = 0	# 类属性，所有实例共享一个类属性
  	def __init__(self, name, age):
      	self.name = name
        self.__age = age
        Dog.count += 1	# 操作类属性的count
    
    def run(self):
      	print('dog is running...')

animal = Animal()	# error
animal = Animal('petty', 2)
print(animal.age) # error
print(animal._Aniaml__age)	# 2
print(animal.getAge())			# 2

dog = Dog('kitty', 1)
animal.run()	# animal is running...
dog.run()			# dog is running...		多态
dog.getAge()	#	error
pet = Dog('pet', 1)

print(isinstance(animal, Aniaml))	# True
print(isinstance(dog, Dog))	# True
print(isinstance(dog, Animal))	# True
print(isinstance(animal, Dog))	# False
print(isinstance(123, int))	# True
print(isinstance(b'123', bytes))	# True
print(isinstance((1,23), (list, tuple)))	# True	判断是否是list或tuple

type(dog)	# 判断对象类型
type(int)
hasattr(dog, '__age')	# False	私有属性访问不到
hasattr(dog, 'name')	# True

dog.count	# 2
pet.count	# 2
Dog.count	# 2
pet.count += 10
print(dog.count)	# 2
print(pet.count)	# 12	访问的是pet的实例属性设置
print(Dog.count)	# 2	
Dog.count += 1
print(dog.count)	# 3
print(pet.count)	# 12	访问的是pet的实例属性设置
print(Dog.count)	# 3

# __slots__
class Cat(Animal):
  	# __slots__ = 'nickname' 会导致构造函数错误，子类的私有属性需要各自配置
    __slots__ = ('nickname', '__age')
    def __init__(self, name, age):
        self.name = name
        self.__age = age	# 实际上被解释器转化为_Cat__age
        
    def getAge(self):
      	print(self.__age)

cat = Cat('biubiu', 3)
cat.nickname = 'biubiu'
cat._Animal__age = 15		# yes
cat._Cat__age = 13			# yes	设置实例的__age
cat.getAge()						# 13	

# @property
class Bird(animal):
  	def __init__(self, name ,age):
      	self.name = name
        self.__age = age
  	
  	@property
    def age(self):					# age属性的读权限
      	return self.__age

    @age.setter
    def age(self, value):		# age属性的写权限，构造函数的执行也会受其影响
      	self.__age = value
    
bird = Bird('faef', 123)
print(bird.age)					# 123	在装饰器的作用下，可直接操作私有属性
print(bird._Bird__age)	# 123
bird.age = 17
print(bird.age)					# 17
print(bird._Bird__age)	# 17	

# 多重继承
class Runnable(object):
  	def run():
      	pass

class Flyable(object):
  	def fly():
      	pass
      
class Dragon(Runnable, Flyable):
  	pass
```



# 多文件

## 模块 

- **python中，一个.py文件就是一个模块。**
- 模块化机制避免了函数名、变量名冲突。
- 包 `package`：按目录来组织模块。
  - 每个包下面都会有一个`__init__.py`，这是必须存在的。否则就只是个普通的目录。
  - 包可以有多级层次，每级都需要有一个`__init__.py`。
  - 模块命名不要和python自带的模块命冲突。
- 作用域
  - `_xxx`属于模块私有变量，**不应该被其他模块引入**。
  - `xxx`属于模块公开的变量，其他模块可引入。
  - `__xxx__`属于模块的特殊变量。

# I/O

- 控制台输入输出

- 文件读写 `file object`
  - 原始二进制文件
  - 缓冲二进制文件
  - 文本文件

# 多任务

https://docs.python.org/zh-cn/3/library/concurrency.html

单任务模型：

采用多任务需要考虑的点：

- 进程/线程切换的开销：与任务的量直接挂钩.
- 任务的类型
  - 计算密集型
  - I/O密集型

## 多进程模型

- `mutipleprocess`
- 多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响.

## 多线程模型

- python中，实际上只能支持的是单进程多线程模型.

- `threading`
- 多线程中，同一进程下所有变量都由其所有线程共享，故进程内任何一个变量都可以被任何一个线程修改.
- 多线程编程较复杂，容易发生冲突，必须用锁隔离全局变量，同时又要防止死锁.
- 锁：`threading.lock()`
  - 全局解释器锁`GIL`：任何python线程执行前，会获得此锁，然后每执行100条字节码，解释器就会自动释放GIF锁，让其他线程有机会执行.
  - 多线程在python中只能交替执行，n个线程运行在n核CPU上，也只能用到1核上。C\C++、Java可以把核心全部用满的.
  - **python虽不能利用多线程实现多核任务，但可通过多进程实现多核任务.**

## 事件驱动模型

现代操作系统对I/O操作已经做了巨大的改进，最大的特点就是支持异步I/O. 如果充分利用操作系统提供的异步IO支持，就可以用<u>单进程单线程模型</u>来执行多任务，这种全新的模型称为事件驱动模型，又称为异步I/O编程模型. 

### 协程

- 可等待对象 `awaitable`
  - 对象内部实现了`__await__()`方法，该方法必须返回一个iterator，用来实现awaitble对象.

- 协程 `coroutine`：python中，单线程的异步编程模型称为协程，由`async/await`实现，是利用协程可基于事件驱动编写多任务程序. 协程由程序主动控制切换，故没有线程切换开销.

  - coroutine属于`awaitable`对象，协程的执行可通过调用`__await__()`并迭代其结果来进行控制.

- 协程函数 `coroutine function`：通过`async def`定义，返回一个`coroutine`.

  - 协程函数体中使用`yield from`表达式会引发SyntaxError. 
  - await表达式、async for语句、async with语句只能在协程函数体中出现，否则会引发SyntaxError.
  - `await`：挂起coroutine的执行以等待一个awaitble对象.

- 异步迭代器 `async iterator`

  - 异步迭代器内部实现`__aiter__()`方法，返回一个异步迭代器.

  - 异步迭代器内部实现`__anext__()`方法，用于调用异步代码.
  - 异步迭代器可以在async for语句中使用.

- 异步上下文管理器

## Master/Worker模型

- Master/Worker可用于分布式多任务.
  - `mutipleprocess.manager` 支持把多进程分布到多台机器上.

```python
### 多线程
import threading

num = 0
lock = threading.Lock()

def change(n):
  	gloable num
    num = num + n
    num = num - n

def run_thread():
		for i in range(1000):
      	lock.acquire()
        try:
          	change(1)
        except Exception as e:
          	logging.exception(e)
        finally:
          	lock.release()
            
t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
```



# 网络编程

https://docs.python.org/zh-cn/3/library/ipc.html

- TCP
- UDP
- HTTP



# 参考

- python官方文档：https://docs.python.org/zh-cn/3/index.html