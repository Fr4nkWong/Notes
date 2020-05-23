---
title: ES6新特性(一)
date: 2017-02-01
category:
- javascript
---
## 前言
随着WEB APP的兴起，JS也随之快速发展。ES6代码在2016年也经过了市场上的考验逐渐被各大公司广泛使用，特别是在Node.js火热的同时高度支持了ES6。

## 基本新特性
### 块级作用域
ES6新增了块级作用域；ES5只有全局作用域和局部作用域。
- 变量声明
 - 防止内层变量**覆盖**外层变量。
 - 防止一些变量**泄露**成全局变量。
 - 允许块级作用域任意嵌套。
- 函数声明
 - 使立即执行匿名函数不再必要。
 - 允许在块级作用域中声明函数；ES5“严格模式”下非法，只能在顶层作用域或函数函数作用域中声明。
 - 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。同时，函数声明还会提升到所在的块级作用域的头部，且其块级作用域之外不可引用该函数。

### const与let声明
- ES6新增let命令，用来声明变量，用法与var类似：
 - 声明的变量**只在**let命令所在的代码块里有效
 - **会在作用域头部被初始化，不过这些变量仅允许在实际声明之后使用。**
 - 满足作用域链查找变量规则，与var相似。
 - let不允许在相同作用域内，**重复声明**同一个变量。
 - let声明的全局变量不是全局对象的属性，故不能通过window.变量名的方式访问。
 ``` bash
 var a = [];
 for (var i = 0; i < 10; i++) {
 a[i] = function () {
 console.log(i); // 变量i是var声明的，在全局范围内都有效。所以每一次循环，新的i值都会覆盖旧值
 };}
 console.log(a[6]()); // 10
 a = [];
 for (let i = 0; i < 10; i++) {
 a[i] = function () {
 console.log(i); // 变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量
 };}
 console.log(a[6]()); // 6
 a = 123;
 if (true) {
 a = 'abc'; // ReferenceError: undefined;该块级作用域已绑定了同名let变量
 let a;
 }
 ```
- ES6新增const命令，用来声明常量：
 - 用法与let相同，没有var提升特性，**只在所在的作用域内的声明位置之后有效**。
 - **只读**，一旦声明**必须初始化**，且其值就不能改变。
 - 对于复合类型的变量(Eg: Object)，变量名不指向数据，而是指向数据所在的地址。**只能保证地址不变**，不能保证数据不变。
 ``` bash
 const PI = 3.1415;
 console.log(PI); // 3.1415
 PI = 3; // TypeError
 const P; // SyntaxError
 ```
### 解构赋值
ES6新增按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构。
- 数组解构赋值
 -适用于var, let, const命令。ES6内部使用严格相等运算符（===），判断一个位置是否有值。故如果一个数组成员**严格等于**undefined，默认值生效。
 - 数组的元素按次序排列，变量取值由其位置决定。
``` bash
var [a,b,c] = [1,2,3]; // a=1,b=2,c=3
var [,,third] = ["foo","bar","baz"]; // third="baz"
var [a, ...z] = [1,2,3,4]; // a=1,z=[2,3,4]
var [x,y, ...z] = ['a']; //x='a',y=undefined,z=[]
var [a,[b],d] = [1,[2,3],4]; // a=1,b=2,c=4
var [foo] = 1; // 报错，等号右边必须是可遍历结构
var [x,y = 'b'] = ['a']; //x='a',y='b';允许指定默认值
var [x = y, y = 1] = []; // ReferenceError;x用到默认值y时，y还未声明
```
- 对象解构赋值
 - 对象属性没有次序，变量必须与属性同名，才能取到值。
 - 对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。**真正被赋值的是变量**，而不是属性。
 - 已经声明的变量用于解构赋值，需要用小括号括起赋值语句。
 - 数组本质是特殊的对象，可进行对象属性的解构。
``` bash
var {a, b} = {a:"a", b:"b"}; // a="a",b="b"
var {a:b} = {a:'a', b:'b'}; // b='a';a是模式，不会被赋值
let a;
({a} = {a:1}); //必须用小括号，否则解析器会将起首的大括号理解为代码块而不是赋值语句
var {x=3} = {x:undefined}; // x=3;undefined !=== null
var {x=3} = {x:null}; // x=null;
```
- (字符串/数值/布尔值/函数参数)解构赋值
 - 字符串被转换成了一个类似数组的对象。
 - 解构时，若等号右边是数值和布尔值，则先转化为对象。**undefined和null无法转化为对象**，故报错。
 - 声明语句不能使用圆括号。函数参数属于变量声明，故不能使用圆括号。
 - **只有赋值语句的非模式部分，可以使用圆括号**。
``` bash
const [a] = 'hello'; // a='h'
let {length:len} = 'hello'; // len=5;数组对象有length属性
[[1,2],[3,4]].map(([a,b]) => a+b); // [3,7]
[1, undefined, 3].map((x = 'yes') => x); // [1,'yes',3];undefined触发默认值
var [(a)] = [1]; // 报错
[(b)] = [3]; // 正常
```
### 模板字符串
- ES6新增模板字符串是增强版的字符串。
- 用 ` 标识。可作普通字符串、多行字符串，可在字符串中嵌入变量。
- 多行字符串，**所有的空格和缩进都会被保留在输出之中**。
- 若模板字符串中使用 ` ，用 \ 转义即可。
- 模板字符串间允许多层嵌套。
``` bash
`In ES6 '\n' is a line-feed.`
console.log(`string text line 1
string text line 2`);
var name = "Frank", time = "today";
`Hello ${name}, how are you ${time}?`
alert`123` // 等同于alert(123)
```
### 正则扩展
- 若RegExp构造函数里第一个参数是正则对象，则第二个参数可另指定修饰符。第二个参数可覆盖掉原有正则对象的修饰符，并返回新正则对象。
- ES6将String.prototype的match()、replace()、search()、split()全部调用RegExp.prototype上新增且对应的方法，即4字节的UTF-16编码。
- ES6新增u修饰符，能正确处理大于\uFFFF的Unicode字符。新增{}表示Unicode字符，这种表示法在正则表达式中必须加上u修饰符才能识别。
- ES6新增了y（粘连）修饰符。全局匹配，后一次匹配都从上一次匹配成功的下一个位置开始。g修饰符只要剩余位置中存在匹配即可，而y修饰符**确保匹配必须从剩余的第一个位置开始**。
就可，而y修饰符确保匹配必须从剩余的第一个位置开始
``` bash
reg = /xyz/g;
reg = new RegExp(/xyz/g); // 返回原有正则对象的拷贝
var reg = new RegExp("xyz","g");//以上ES5，ES6皆可用
reg = new RegExp(/xyz/,"g"); //仅ES6可用
/\u{61}/u.test('a'); // true
/\u{61}/.test('a'); // false
var a = 'aaa_aa_a';
var r1 = /a+/g;
var r2 = /a+/y;
r1.exec(s); //["aaa"]
r2.exec(s); //["aaa"]
r1.exec(s); //["aa"]
r2.exec(s); //null
```
### 数组扩展
- Array.from()可将类似数组(具有length属性)的对象、可遍历对象(包括ES6新增数据结构Set和Map)转化为数组。
- 扩展运算符...也可以通过调用其背后的遍历器接口(Symbol.iterator)将其转化为数组。
- 数组的空位是指数组的某一个位置没有任何值。但**空位不是udefined，undefined也算一个值**。
- ES6会将空位转化为undefined(包括扩展运算符)。ES5对于空位的处理与ES6不一致，大多情况下会忽略空位。
``` bash
let boj = {'0':'a','1':'b','2':'c',length:3};
var arr1 = [].slice.call(obj); // ['a','b','c'];ES5数组转化
var arr2 = Array.from(obj); // ['a','b','c'];ES6数组转化
function(){var args = [...arguments];} // 将某些数据结构转为数组
Array.of(3,11,8); // [3,11,8];将一组值转为数组
for(let [index,elem] of ['a','b'].entries()){
	console.log(index,elem);
} // 0 "a",1 "b"
Array(3); // [,,];
[,'f'].forEach((x,i) => console.log(i)); // 1;ES5忽略空位
Array.from(['a',,'c']); // ['a',undefined,'b'];ES6空位转为undefined
```
### Symbol
- ES5中属性名都是字符串。为防止属性名冲突，ES6引入一种新的原始数据类型Symbol，表示**独一无二**的值。Symbol值通过Symbol()生成，其**值可修改**。
- ES6中对象的属性名有两种类型：字符串类型、Symbol类型。
- Symbol值不是对象，故不能添加属性。其类似于字符串数据类型，但不能与其他类型值进行运算，否则报错。
``` bash
let a = Symbol();
type of a; // "symbol"
a; // "Symbol()"
var b = Symbol();
a == b; // false
a === b; // false
var c = Symbol("Frank");
"I'm "+ c; // TypeError
`I'm ${c}`; // TypeError
var obj = {};
obj.a = 'Frank'; // 字符串类型属性
obj[a] // undefined;Symbol类型属性
obj['a'] // "Frank";字符串类型属性
let s = Symbol();
const DATA = System('data');
let o = {
	[a]: function(arg){},
	[b](arg){},
	INFO: Symbol('info') // 定义常量
	DATA
};
```
### Other
``` bash
2 ** 3; // 8
let b = 2;
b **= 3; // 8;等同于a = a*a*a
```
## 小结
虽然ES6只是ES5的版本更新，但是在各种细节上注入了不少新的元素，相比于ES5与其他语言形式上的格格不入，ES6更具有标准语言的样式。同时ES6也带来了很多新的编码姿势。