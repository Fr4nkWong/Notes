---
title: JS深浅拷贝
date: 2017-05-01
category:
- javascript
---
## 前言
JS中有**7种数据类型**：Boolean, Number, String, Object, Null, Undefined, Symbol。
我们大可把Boolean,Number,String分为一类，它们原理相同，只是单纯值上的传递和拷贝，判断是否相同仅仅是同过值进行判断。
而Object不同，对象实际上是一个指向一个地址的指针，该地址是一块内存空间的地址。Object是通过指向对应内存空间的地址是否相同进行判断。这是Boolean,Number,String与Object的根本不同。而Function,RegExp,Array,Date等常用的类皆是继承Object而来。So...
``` bash
	var a = 1;
	var b = a; //b=1
	console.log(a == b); //true
	console.log(a === b); //true
	a = 2; //a=2,b=1
	console.log(a == b); //false
	console.log(a === b); //false
```

## 浅拷贝 Shallow copy
既然是判断内存空间是否是指向同一地址...那么若是指向同一地址，那么若内存内部存储的值若通过指向它的众多指针其中之一来访问并修改了它里面的值，那么其他指针呢?还是原来的值吗？不会！会跟随一起变化吗？是的！为什么呢？因为他们只是变量名不同（其实就是指针，学过C/C++就应该理解），实际代表的是同一个东西（内存）。Eg: 我有个中文名和一个英文名各不相同，但是他们都指向我本身，Frank变帅了和王某人变帅了是一个意思，其实就是我变帅了...
``` bash
let obj = {
	name:"Frank"
}
let o = obj;
o.name; // "Frank"
o.name = "Morningstar";
obj.name; // "Morningstar"

```

## 深拷贝 Deep copy
深拷贝即两个变量（指针）指向两个不同的内存空间，不过存的内容是相同的。那么其中一个变量改变，另一个的值是不会随之改变的，两者毫无关联。Eg: 我有一个双胞胎哥哥，我们长得一模一样，但是有个女生喜欢他，不代表她喜欢我啊...
``` bash
	let a = [1,2]; 
	let b = [3];
	let c = a.concat(a,b); //[1,2,3]; a,b并没有改变
	let d = c.slice(0,2); //[1,2]; c并没有改变 
	let e = Object.assign([],a); //[1,2]
	console.log(a == d); //false; 并不相等！
	console.log(a == e); //false
```

## 小结
无论什么语言，深浅拷贝都是基础也是必须要掌握的比较重要的知识点。在这里只分析了最简单的情况方便理解，若多数据类型进行嵌套很容易弄混以至于出错或者一些隐藏着的bug。