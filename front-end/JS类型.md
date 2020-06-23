---
title: JS类型
date: 2017-03-10
category: 
- javascript
---
## 前言
既然要判断JS的数据类型，那首先得知道JS到底有什么数据类型吧...
基本类型：String, Number, Boolean, Symbol
特殊类型：undefined, null
引用类型：Object, Function, Array, RegExp, Date...
类型就总结到这里了，废话不多说，上干货！

## 数据类型

## 判断方法
### 0x01: typeof
typeof 返回一个表示数据类型的字符串。返回结果包括："number","boolean","string","object","undefined","function"六种数据类型。
``` bash
typeof ''; // "string"
typeof undefined; // "undefined"
typeof null; // "object"
typeof []; // "object"
typeof new Function(); // "function"
typeof new Date(); // "object"
typeof NaN; // "number"
```
### 0x02: instanceof
实际上这个命令是在检测原型，旨在判断前者的__protot__属性是否指向了后者的原型，可以联想到JS继承关系。再延伸到原型链，那么只要后者在前者的原型链前，那么就返回true。So，instanceof 只能用来判断两个对象是否属于原型链的关系，而不能获取对象的具体类型。
``` bash
[] instanceof Array; // true
{} instanceof Object; // true
[] instaceof Object; // true
null instanceof Object; // false
Object instanceof null; // TypeError
```
### 0x03: constructor
当一个函数function被定义时，JS引擎会为该函数添加prototype原型，再为prototype原型添加constructor属性，并让其作为引用指向该函数。
``` bash
function fun(){}
F.prototype; // Object{constructor:function fun(),__proto__:Object}
let f = new fun();
f.constructor == fun; // true;
```
当该函数被当做构造函数来创建对象时，创建的新对象就会以该函数名为类型。
- null和undefined是无效的对象，因此是不会有constructor存在的，这两种类型的数据需要通过typeof来判断。
- **JS对象的constructor是不稳定的**，这个主要体现在自定义对象上。当开发者重写prototype后，原有的constructor会丢失，constructor默认被重新赋值一个{}，而{}是new Object()的字面量。所以一般在重写prototype后，会在{}里写一个constructor属性，使其指向需要的构造函数。

``` bash
''.constructor == String; // true
new Number(1).constructor == Number; //true
new Error().constructor == Error; // true
document.constructor == HTMLDocument; // true
window.constructor == Window; // true

function F(){}
F.prototype = {a:"1"}; // Object{a:"1"}
var f = new F();
f.constructor == F; // false
f.constructor; // function Object(){[native code]}
F.prototype = {
	constructor:F, // 重新设置constructor引用,保证实例对象类型不被改写
	a:'1'
}
```
### 0x04: Object.prototype.toString
toString()是Object原型对象上的一个方法，默认返回其调用者的具体类型。延伸一下，就是返回运行toString()时，this所指向的对象类型。返回格式为 [Object xxx] :
``` bash
Object.prototype.toString().call(''); // [Object String]
Object.prototype.toString().call(undefined); // [Object Undefined]
Object.prototype.toString().call(null); // [Object Null]
Object.prototype.toString().call(new Error()); // [Object Error]
Object.prototype.toString().call(document); // [Object HTMLDocument]
Object.prototype.toString().call(window); // [Object global]
undefined.toString(); // Error
null.toString(); // Error
```
在此，我之所以不用对象直接调用toString()是因为很多对象大多都实现了自身的toString()方法，使对象无法沿着原型链向上找到Objcet.prototype.toString()，所以我用call()手动调用。

## 规范类型

## 小结
其实能判断JS数据类型的方法还有不少，不过大都大同小异，其核心原理就是利用了这4种方法。故我也只总结了以上4种，第一种是最底层封装好的命令，剩下三种其实都利用了原型及原型链的性质。原型及原型链是JS的重要特性，对其的理解和运用在很大程度决定了你在JS上的造诣。