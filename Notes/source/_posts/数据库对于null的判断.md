---
title: 数据库对于null的判断
date: 2017-07-17
categories:
	- database
---

## 前言
在实训培训Oracle数据库操作时，遇到了这样一个题目：在公司里找到和Frank拿一样工资一样奖金的人并显示他们的名字、工资、奖金。
我理所当然地写下: 
``` bash
select name, sal, comm from demp 
where sal = (select sal from demp where name = 'Frank') and 
comm = (select comm from demp where name = 'Frank');
```
好的，报错！然后我发现demp表里Frank这一行的comm列是null值，只要是没有奖金的都是为空！我意识到这个等号是不可能和null匹配的，于是写下：
``` bash
select name, sal, comm from demp 
where sal = (select sal from demp where name = 'Frank') and 
comm is (select comm from demp where name = 'Frank');
```
好的，报错！其他数据库我是不知道，反正Oracle是没这种操作的...
## Oracle解决办法
其实症结已经找到了，就是等号无法处理Null，而is也不是这么用的... Oracle里有一种内置的方法或者操作符是可以处理的，比如：nvl(a,b)。可以理解为如果a为null，那么它可以转化为b进行处理，否则则返回a的结果。
``` bash
select name, sal, comm from demp 
where sal = (select sal from demp where name = 'Frank') and 
nvl(comm) = nvl((select comm from demp where name = 'Frank'),0);
```

## MySQL解决方法
问题是解决了，让我不禁开始延伸...nvl()是Oracle的内置方法，那么其他的数据库是没有这个方法的，当前最火的MySQL是怎么解决这个问题呢？MySQL当然也是有自己的内置方法的，比如：ifnull(a,b)，行为模式和nvl()是一样的。
``` bash
mysql> select ifnull(1,10);
+--------------+
| ifnull(1,10) |
+--------------+
|            1 |
+--------------+

mysql> select ifnull(null,10);
+-----------------+
| ifnull(null,10) |
+-----------------+
|              10 |
+-----------------+

select name, sal, comm from demp 
where sal = (select sal from demp where name = 'Frank') and 
nvl(comm) = nvl((select comm from demp where name = 'Frank'),0);
```
MySQL还有一个内置方法用于判断结果是否为Null，返回值是1表示null返回0表示不为空。
``` bash
mysql> select isnull(null);
+--------------+
| isnull(null) |
+--------------+
|            1 |
+--------------+

mysql> select isnull(1);
+-----------+
| isnull(1) |
+-----------+
|         0 |
+-----------+
```
## 小结
无论是前端还是后台，数据库都是基本技能。知识对于常人几乎是无穷无尽的，要想在有限的时间掌握更多就只能举一反三，对于知识、项目要有延伸性。