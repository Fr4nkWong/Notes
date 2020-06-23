---
title: ES6新特性(二)
date: 2017-02-20
category:
- javascript
---
## 前言
相比于基础的一些小改动，比较接触语言核心的更新莫过于函数、对象。下面是对于ES6函数新特性的详细归纳。
## 函数新特性
### 函数参数
- 可为函数参数指定默认值即直接写在参数定义后面(ES5不行)。
``` bash
function fun(x = 'Hey',y){
	console.log(x,y);
}
fun('Frank'); // Hey Frank
```
- 参数变量是默认声明，故不可用let,const再次声明。
``` bash
function fun1(a,{b='',c='wong',d={}}){
	console.log(c);
}
fun1('frank'); // 报错 
fun1('frank',{}); //"wong";第二个参数不可忽略
function func2(a,{c="wong"}={}){
	console.log(c);
}
fun2('frank'); // "wong";可省略第二个参数写法
```
- 函数参数可与对象的结构默认赋值结合。If so,只有当函数的传参为对象时，才能解构赋值。
- 传入参数undefined，将触发该参数等于默认值，null则没有这个效果。
``` bash
function f1({a=0,b=0}={}){
//参数默认值是空对象，设置了对象解构赋值默认值
	return [a,b];
}
function f2({a,b}={a:0,y:0}){
//参数默认值是有具体属性的对象，没设置对象解构赋值默认值
	return [x,y];
}
f1({a:1}); //[1,0]
f2({a:1}); //[1,undefined]
function f(x=1,y){return [x,y];}
f(); // [1,undefined]
f(,1); // 报错
f(undefined,1); // [1,1]
```
### 函数作用域
- 函数的length属性值 = 函数参数个数 - 指定默认值的参数个数。
- 关于函数作用域问题一直都是核心。**ES6里要理解当前函数作用域与全局作用域的差别，特别是函数参数**。函数参数默认值可为一个匿名函数、对象。
``` bash
var x=1;
function f3(x,y=x){
//函数作用域中先定义x，其x赋值给y
	console.log(y);
}
let a=1;
function f4(b=a){
//函数作用域内并没有a，作用域链向上寻找a
	let a=2;
	console.log(b);
}
f3(2); // 2
f4(); // 1
function foo(x,y=function(){x=2;}){
//调用foo时,参数x为undefined,后来被赋值为2;后foo内部重新声明并赋值x，前后两个x是不一样的，互不影响
	var x = 3;
	y();
	console.log(x);
}
function foo1(x,y=function(){x=2;}){
//
	x = 3;
	y();
	console.log(x);
}
foo(); // 3
foo1(); // 2
function throwFuck(){
	throw new Error('Fuck');
}
function foo2(mbp = throwFuck()){
	return mbp;
}
foo2(); // Fuck
```
### Rest参数
- ES6引入rest参数(...变量名)，用于获取函数的多余参数。rest参数搭配的变量是一个数组，**多余的参数序列存在数组中**。rest参数之后不能再有其他参数且只能是**最后一个参数**，否则报错。
- **函数的length属性不包括rest参数**。
``` bash
function sort(){
//arguments变量写法
	return Array.prototype.slice.call(argument).sort();
}
//rest参数写法
const sort = (...numbers) => numbers.sort();
```
### 扩展运算符
- 扩展运算符(...)**将一个数组转为用逗号分隔的参数序列**，如同rest参数用法的逆运算。
 - 扩展运算符用于数组赋值，**只能放在数组里的最后一位**，否则报错。
 - **任何具有iterator接口的对象，都可以用扩展运算符转为数组**，否则不行！

``` bash
console.log(1,...[2,3,4],5); // 1 2 3 4 5
function f(a,b,c,d,e){}
var args = [0,1];
//扩展运算符与正常函数参数结合使用
f(-1,..args,2,...[3]);
//ES5写法
function f1(x,y,z){}
args = [0,1,2];
f1.apply(null,args);
//ES6写法
function f2(x,y,z){}
f2(...args);
[1,2].concat(array); // ES5数组合并
[1,2, ...array]; // ES6数组合并
const [first, ...middle,last] = [1,2,3,4,5]; // 报错
const [first, ...rest] = [];
console.log(first); // undefined
console.log(rest); // []
(new Function).name // "anonymous";
(function(){}).bind({}).name // "bound ";bind返回的函数，name属性值会加上'bound'前缀
```
### 箭头函数
- ES6允许使用(=>)定义函数(即箭头函数)。
 - 若箭头函数不需要参数或需要多个参数，用一个小括号代表参数部分。
 - 若箭头函数的代码块部分多于一条语句，用大括号将其括起，且用return语句返回。
 - 若箭头函数直接返回一个对象，必须在对象外面加上小括号。
 - **不可作构造函数**，即不可用new命令，否则报错。
 - **不可使用arguments对象**，其在函数体内不存在，用rest参数代替。
 - 不可使用yield命令，**箭头函数不能作Generator函数**。
 - **箭头函数体内的this对象，是定义时所在的对象，而不是使用时所在的对象**。
``` bash
//ES5写法
var f = function(v){return v;};
var f1 = function(){return 5;}
//ES6写法
var f = v => v;
var f1 = () => 5; 
var sum = (a,b) => {return a+b;}
var get = a => ({b:'b',c:'c'});
//this用法的不同情况
function Timer(){
	this.a = 0;
	this.b = 0;
	setInterval(() => this.a++, 1000);
	setInterval(function(){
		this.b++;
	},1000);
}
var timer = new Timer();
setTimeout(() => console.log(timer.a),3100); // 3
setTimeout(() => console.log(timer.b),3100); // 0
```
### 尾调用
- 尾调用不一定出现在函数尾部，只要是**最后一步操作**即可。
- 递归很消耗内存，需同时保存很多调用帧，故很容易发生栈溢出。**但尾递归只存在一个调用帧，永远不会栈溢出**。
- ES6尾调用优化**只在**严格模式下开启，正常模式是无效的。
``` bash
function factorial(n){
	if(n === 1) return 1;
	return n*factorial(n-1);
}
function factorial1(n, total=1){
	if(n === 1) return total;
	return factorial(n-1, n*total);
}
factorial(5); // 120
factorial1(5); // 120
```
## 小结
关于ES6函数的新特性有不少是《ES6新特性(一)》里的东西在函数传参、调用时的展现。从语法上讲，ES6使JS写法越来远规范，但又越来越灵活。从性能上来说，ES6可以有比ES5更高效更简洁的写法，比如箭头函数、默认参数、**尾调用的优化** :)