---
title: 作用域链&闭包&this
date: 2017-06-01
category:
- javascript
---
## 前言
从接触Javascript以来，除了原型链那套体系最让我感到惊心动魄的就是闭包了。闭包还能牵扯出更多的知识点（全是细节，容易踩坑）。比如：作用域、作用域链和this指针。本文就由基础到高级分析梳理一下我对JS的分析。
## 作用域
变量在声明它们的函数体以及这个函数体嵌套的任意函数体内都是有定义的。**JS有4种作用域：全局作用域、函数(局部)作用域、eval()作用域、块级作用域(ES6新增)。**这里必须提到一点：**var是无视块级作用域的，let、const才遵守块级作用域的规则。**
``` bash
function a(){
	name = "frank"; // 没有var或let声明，让window自动获得
	console.log(name);
	if(true){
		var birth = "1996";
		const sex = "male";
		let age = 21;
	}
	console.log(birth); // "1996"
	console.log(sex); // Error
	console.log(age); // Error
}
a(); // "frank"
console.log(name); // "frank"
```

## 作用域链
作用域里可以嵌套多个作用域，当在某个作用域里访问某个变量时，会从当前作用域开始查找，若不存在则沿着作用域链向上查找变量，直到查找到第一个满足条件的变量。若查找到全局变量还未找到，便返回undefined。其实作用域链可以理解为栈，栈底为全局作用域，同步顺序执行代码，有新的作用域(执行函数、块级作用域)就入栈，作用域执行完就出栈，代码执行完全局作用域也就出栈了。
``` bash
var a = "frank";
let b = 1;
const d = 2;
function c(){
	console.log(a); // "frank"
	console.log(b); // 1
	console.log(d); // 2
	console.log(e); // undefined
}
```

## this用法
this总是指向当前函数作用域的拥有者对象，故只有在运行时才能确定其具体的指向（调用对象）。
this对象使用场景：
- 作为函数调用，this绑定全局对象，浏览器环境全局对象为 window 。
- 内部函数被赋予一个变量进行调用时的this绑定全局对象
（应该绑定到其外层函数对应的对象上，这是 JavaScript的缺陷，用that替换或者使用闭包）。
- 作为构造函数使用，this 绑定到新创建的对象。
- 作为对象方法使用，this 绑定到该对象。
- 使用call()、apply()、bind()函数。

call()、apply()、bind()是JS中Function对象自带的三个方法，主要是用于改变this的指向。
 - call(caller[,args...]): this默认为window对象；若传递基本类型变量，this指向其对应的包装对象；若传递对象，则指向该对象；若是函数，则this指向此函数的应用。
 - apply(caller[,args[]]): 第二个参数必须是一个**数组**。但在给调用函数传参数的时候是以参数列表形式传递。
 - bind(): **不会自动执行对应的函数(与call(),apply()不同)。一旦传入有效对象，该函数运行期间this始终指向此对象(无论中途是否用call、apply函数)。**

``` bash
window.name = "frank";
function a(){
	console.log(this.name);
}
a(); // "frank"
var b = {name: "morningstar"};
b.call(b); // "morningstar"
```

## 作用域延长
其实在我看来call(),apply(),bind()也算是作用域延长的一种方法了，不过除此以外还有2中方法：with和catch。不过我在看JS红宝书的时候，书上也明确提到了尽量少或者不要使用with，因为很影响性能特别是在设置了属性值时。
``` bash
var a = "frank";
function b(){
  var a = "adam";
  function c(){
    console.log(a); // adam
    with(d){
 	  console.log(a); // morningstar
	}
	console.log(a); // adam
  }
  var d = {a:"morninstar"};
  c(d);
}
b();

a = new Error('a');
try{
  throw new Error('b');
}catch(e){
  console.log(e.message); // b
}
```

## 闭包Closure
闭包是指有权访问另一个函数作用域的变量的函数。在C或JAVA中，函数返回之后，所有局部变量都不能再被访问，因为栈帧已经被销毁了。这里简单提一下JS中的**GC(垃圾回收)机制：在 Javascript 中，如果一个对象不再被引用，那么这个对象就会被 GC 回收，否则这个对象一直会保存在内存中。**
- 特性:
 - 函数返回嵌套函数形成闭包。
 - 闭包内部可以访问其外部(主函数)的参数和变量。
 - 其外部(主函数)的参数和变量在被闭包引用时不会被JS垃圾回收机制回收(变量的值始终保持在内存中)。

- 优点：
 - 可避免全局变量污染
 - 允许函数私有成员的存在
 - 允许变量常驻内存

- 缺点：
 - 变量常驻内存，增大内存使用量，使用不当很容易造成内存泄漏、网页性能变差问题。
 - 闭包使用问题：1.由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

``` bash
/*例子1*/
function f1(){
 var n=999;
 nAdd=function(){n+=1}//全局变量
 function f2(){
  alert(n);
 }
 return f2;
}
/*f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，
因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制回收。*/
var result=f1(); 
result(); // 999
nAdd();
result(); // 1000
/*例子2*/
var name = "The Window";
var object = {
 name : "My Object",
 getNameFunc : function(){
  return function(){
   return this.name;
　};
 }
};
var object1 = {
 name : "My Object",
 getNameFunc : function(){
 var that = this;
 return function(){
 return that.name;
　};
 }
};
alert(object.getNameFunc()()); //"The Window"
alert(object.getNameFunc()()); //"My Object"
```
在实际应用中，闭包往往和匿名函数相结合使用。Eg:立即执行函数。
``` bash
(function(document){
    var viewport;
    var obj = {
        init:function(id){
           viewport = document.querySelector("#"+id);
        },
        addChild:function(child){
            viewport.appendChild(child);
        },
        removeChild:function(child){
            viewport.removeChild(child);
        }
    }
    window.jView = obj;
})(document);
//等价于以下代码
var f = function(document){
	//...
};
f(document);
```

## 小结
之所以把以上几点放在一起总结是它们之间的联系很紧密，无法完全单独分开分析。还有JS的执行环境和活动对象其实闭包也有涉及到，不过在这里暂且不提，望日后能更精炼详细地补充。d