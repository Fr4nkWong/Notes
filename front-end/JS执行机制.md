---
title: JS执行机制
date: 2019-04-25
category:
- javascript
---
## 前言

《重学前端》学习笔记...

## 任务

What：传给JS引擎执行的代码段。

How：`MacroTask -> (MicroTask) -> render -> MacroTask ...`

- `MacroTask`：又称`Task`
  - 宿主发起的任务称为<u>宏任务</u>。
  - JS引擎等待宿主环境分配宏观任务，称为<u>事件循环</u>。其底层实现是由`C\C++`实现的一个独立线程中的循环。`宏观任务队列 = 事件循环`
  - 每一个宏任务会被从头到尾执行完毕，不会执行其它；
  - 浏览器为了能够使得JS内部宏任务与DOM任务能够有序的执行，会在一个task执行结束后，在下一个 task 执行开始前，对页面进行重新渲染；
- `MicroTask`：又称`Job`
  - JS引擎发起的任务称为<u>微任务</u>。ES6引入Promise后，JS引擎才有了发起(微观)任务的能力。
  - Promise会在宏观任务中产生异步代码，异步代码作为微观任务放入所属宏观任务的微观任务队列中。

- 触发异步任务：
  - `setTimeout()`：是宿主(浏览器)API，产生宏任务；
  - `Promise`：在所属宏任务内产生微任务微任务；
  - `async/await`：await等待一个Promise对象/async函数返回一个Promise对象；CO库基于`generator/iterator`实现了的类似async/await的功能，但其本身并非异步代码；
  - `process.nextTick()`：node环境下，process.nextTick()在微任务队列中优先级高于Promise；
  
- 异步执行分析

  - 首先我们分析有多少个宏任务；
  - 在每个宏任务中，分析有多少个微任务；
  - 根据调用次序，确定宏任务中的微任务执行次序；
  - 根据宏任务的触发规则和调用次序，确定宏任务的执行次序；
  - 确定代码执行顺序；

```javascript
// eg1：a b e c d
setTimeout(() => console.log('d'), 0);
new Promise((resolve, reject) => {
	console.log('a');
  var begin = Date.now();
  while(Date.now() - begin < 1000); // 延迟1S
	resolve();
}).then(() => console.log('c'));
console.log('b');
console.log('e');

// eg2：a b d c e
setTimeout(() => {
	console.log('d');
}, 0);
setTimeout(() => {
	console.log('e');
}, 6000);
function sleep(duration) {
    return new Promise(function(resolve, reject) {
        console.log("b");
        setTimeout(resolve,duration); // 创建宏任务
    });
}
console.log("a");
sleep(5000).then(()=>console.log("c"));

/*
* async/await
* async函数对外返回的Promise对象，必须等到内部所有await命令后面的Promise对象执行完，才会发生状态改变(类似Promise.all方法)，除非遇到return或抛出错误；
* async函数内部的return语句返回的值会成为then()方法回调函数的参数；
* await后跟Promise对象，返回对象的结果；若不是Promise对象，则直接返回对应值；
*/
// eg1
function sleep(ms, name) {
    return new Promise((resolve, reject) => {
        console.log('sleep:'+name);
        resolve('resolve:'+name);
    });
}
async function foo(name){
    p = await sleep(2000, name); // 返回undefined
    // p.then((data) => console.log('then:'+name); // Illegal return statement
    console.log(p);
    console.log(name);
}
async function foo2(){
    await foo("a");
    await foo("b");
  	console.log('d');
}
foo2();
console.log('c');

// eg2
function sleep(ms, name) {
    return new Promise((resolve, reject) => {
        console.log('sleep:'+name);
        setTimeout(resolve('resolve:'+name), ms);
    });
}
async function foo(name){
    p = await sleep(2000, name); // 返回undefined
    // p.then((data) => console.log('then:'+name); // Illegal return statement
    console.log(p);
    console.log(name);
}
async function foo2(){
    await foo("a");
    await foo("b");
  	console.log('d');
}
foo2();
console.log('c');
```
## 函数的执行

### 函数

```javascript
/*
* 普通函数：function关键字定义
* 可new：prototype -> constructor
*/
function func() {}
var func = function() {}

/*
* 箭头函数：用 => 运算符定义的函数
* ES6引入，不可new，即不可作构造器
*/
const func = () => {};

/*
* 生成器函数：用 function* 定义的函数
* prototype里无constructor，不可new
*/
function* func() {
	...
	yield;
	...
}

/*
* 用class关键字定义的"类"，本质上是函数(对象)，相当于其实例的__proto__
* 方法：在class中定义的函数
* 只能通过new
*/
class func {
	constructor(argv) {...}
    static method1() {}
	method2() {}
    * [Symbol.iterator]() {...} // 模拟iterator函数
}

// 用new时，等同于下面
function func(argv) {
	// constructor...
	func.prototype.method = function() {};
}

/*
* 异步函数：async关键字加上普通函数、箭头函数和生成器函数
* 不可new，即不可作构造器
*/
async function func() {}
const func = async function() {}
const func = async () => {};
async function* func() {}
class func {
    async method() {}
}
```
### 闭包
What：`closure`

- 在编译原理中，它是处理语法产生式的一个步骤；
- 计算几何中，它表示包裹平面点集的凸多边形(译为凸包)；
- 在编程语言中，它表示一种函数。注意：`闭包 != 普通函数`，闭包本质上一个<u>绑定了执行环境</u>的函数。
- 在JS中，闭包是函数和声明该函数的词法环境的组合；这个环境包含了这个闭包创建时所能访问的所有局部变量；

作用：

- 不让不同的执行上下文对代码的执行结果造成影响；
- 过度使用导致内存溢出；

<img src="./img/JS函数执行过程.png" />

```javascript
// eg1
function makeFunc() {
    var name = "test";
    function displayName() {
        console.log(name);
    }
    return displayName;
}

var myFunc = makeFunc();
myFunc(); // test

// eg2
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12

// eg3
var Counter = (function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }   
})();

console.log(Counter.value()); /* logs 0 */
Counter.increment();
Counter.increment();
console.log(Counter.value()); /* logs 2 */
Counter.decrement();
console.log(Counter.value()); /* logs 1 */
```
### 执行上下文
What：`Excutable Code and Execution Contexts`
- JS标准把一段代码(包括函数)，执行所需的所有信息定义为`执行上下文`。
- JS用一个栈来管理执行上下文，栈中每一项又包含一个包含词法环境的链表表头。
- ES3 context
	- scope：**作用域(链)**。
	- variable object：变量对象，用于存储变量的对象。
	- this value：this值。
- ES5 context：对命名方式进行了改进。
	- lexical environment：词法环境，当获取变量时使用；作用域划入其中`environment record`。
	- variable environment：变量环境，当声明变量时使用。
	- value：this值。
- ES2018 context
	- lexical environment：词法环境，当获取变量或this值时使用。
	  - environment records：`[[ThisBindingStatus]]`
	  - lexical environment operations：`[[ThisMode]]`
	- variable environment：变量环境，当声明变量时使用。“在the global object-”
	- code evaluation state：用于恢复代码的执行位置。
	- Function：执行的任务是函数时使用，表示正在被执行的函数。
	- ScriptOrModule：执行的任务是脚本或模块时使用，表示正在被执行的代码。
	- Realm：使用的基础库和内置对象实例。
	- Generator：仅生成器上下文有这个属性，表示当前生成器。

How：函数调用(切换执行上下文，入栈出栈)。

```javascript
/*
* 作用域
* ES6引入块级作用域：for if switch try/catch/finally
* 变量声明与赋值: var let const
*/
// eg1
function outFun2() {
    variable = 1;
    var inVariable2 = "内层变量2";
}
outFun2(); //要先执行这个函数，否则根本不知道里面是啥
console.log(variable); // 1

// eg2
var outVariable = "我是最外层变量"; //最外层变量
function outFun() { //最外层函数
    var inVariable = "内层变量";
    function innerFun() { //内层函数
        console.log(inVariable);
    }
    innerFun();
}
console.log(outVariable); // 我是最外层变量
outFun(); // 内层变量
console.log(inVariable); // inVariable is not defined
innerFun(); // innerFun is not defined

// eg3
console.log(a); 
if(true) {
	var a = 1;
	let b = 2;
	const c = 3;
	console.log(a);
}
console.log(a); // 1
console.log(b); // b is not defined

/*
* 立即执行的函数表达式(IIFE)： 通过创建一个立即执行的函数，来构造一个新的域，从而控制var的范围
* 作用：表达式中的变量不能从外部访问，防止变量外泄和暴露、污染全局变量
*/
var a = 1;
console.log(window.a); // 1

(function() {
	var a;
	...
}());

(function() {
	var a;
	...
})();

void function() {
	var a;
	...
}();
    
var result = (function() {
	var a = 1;
	console.log(a);
	return a;
})();

/*
* Realm：ES2018引入
*/
var iframe = document.createElement('iframe')
document.documentElement.appendChild(iframe)
iframe.src="javascript:var b = {};"

var b1 = iframe.contentWindow.b;
var b2 = {};

console.log(typeof b1, typeof b2); //object object
console.log(b1 instanceof Object, b2 instanceof Object); //false true
```
### this
What：是js的一个关键字，也是执行上下文中很重要的一个组成部分。JS标准定义了`[[thisMode]]`私有属性：
- `lexical`：表示从上下文中找this，对应箭头函数。
- `global`：表示this为undefined时，取全局对象，对应了普通函数。
- `strict`：`"use strict"`严格模式下，this严格按照调用时传入的值，可能为null或undefined。注意，<u>class里的方法默认严格模式</u>。

How：函数调用是**上下文切换**的一个主要场景，上下文切换很可能导致语句执行结果出现非预期的结果，主要的差异在于`this`。函数创建新的执行上下文中的词法环境记录时，会根据 `[[thisMode]]`来标记新纪录的 `[[ThisBindingStatus]]` 私有属性。代码执行遇到 this 时，会逐层检查当前词法环境记录中的 `[[ThisBindingStatus]]`，当找到有 this 的环境记录时获取 this 的值。

内置函数：指定函数调用时传入的this值；

- `Function.prototype.call(object, [...])`；
- `Function.prototype.apply(object, …)`；
- `Function.prototype.bind(object, …)`： 
  - 与call()、apply()不同，bind()有返回值且不会自动执行对应的函数；
  - 返回一个原函数的拷贝，并拥有指定的this值和初始参数；
  - 函数被new关键字使用时，bind()失效；

**new与this**：

- 以构造器的 `prototype` 属性**（注意与私有字段 `[[prototype]] `的区分）**为原型，创建新对象；
- 将 this 和调用参数传给构造器并执行；
- 如果构造器返回的是对象，则返回；否则返回第一步创建的对象；

|   函数类型    |  New   |
| :-----------: | :----: |
|   普通函数    | 新对象 |
|   箭头函数    |  报错  |
|     方法      |  报错  |
| Generator函数 |  报错  |
|     class     | 新对象 |
| 异步普通函数  |  报错  |
| 异步箭头函数  |  报错  |
|  异步生成器   |  报错  |

```javascript
// eg1
function showThis(){
    console.log(this);
}

var o = {
    showThis: showThis
}

showThis(); // window或global
o.showThis(); // o

// eg2
const showThis = () => {
    console.log(this);
}

var o = {
    showThis: showThis
}

showThis(); // global
o.showThis(); // global

// eg3
"use strict"
function showThis(){
    console.log(this);
}

var o = {
    showThis: showThis
}

showThis(); // undefined
o.showThis(); // o

// eg4
var o = {}
o.foo = function foo(){
    console.log(this);
    return () => {
        console.log(this);
        return () => console.log(this);
    }
}
o.foo()()(); // o, o, o

var o = {}
o.foo = function foo(){
    console.log(this);
    return () => {
        console.log(this);
        return function() {
          console.log(this);
        }
    }
}
o.foo()()(); // o, o, window

var o = {}
o.foo = function foo(){
    console.log(this);
    return function() {
        console.log(this);
        return () => {
          console.log(this);
        }
    }
}
o.foo()()(); // o, window, window

/*
* this内置函数
* 函数类型如箭头、class是不接收this的，call/apply/bind无法改变其this，但可以实现传参。
*/
function foo(a, b, c){
    console.log(this);
    console.log(a, b, c);
}
foo.call({}, 1, 2, 3);
foo.apply({}, [1, 2, 3]);
const func = foo.bind({}, 1, 2, 3);
foo(1, 2, 3);
func();
```

## 语句

### Completion类型

JS中有一个标准类型`Completion Record`，用来表示语句执行的完成状态。它有三个字段：

- `[[type]]`：表示完成的类型，分别是`break, continue, return, throw, normal`
- `[[value]]`：表示语句的返回值，若没有，则表示empty；
- `[[target]]`：表示语句的目标，通常是一个JS标签；


### 普通语句
What: 在 JS中把不带控制能力的语句称为普通语句。普通语句有以下几种：
- 声明类语句
  - var声明
  - let声明
  - const声明
  - 函数声明
  - 类声明
- 表达式语句：只有表达式语句会产生`[[value]]`
- 空语句
- with语句
- debugger语句

How: 普通语句执行后，会得到`[[type]]`为normal的`Completion Record`，JS引擎遇到这样的`Completion Record`会继续执行下一条语句。
Tips：控制台显示的就是语句的`Completion Record`的`[[value]]`；

```
var a = { name: 'foo' };
let b = 2;
const c = 'abc';
function d() {}
var e = () => 3;
let f = function() {};
class g {}

/*
* 表达式语句
* 由运算元和运算符(可选)构成，并产生运算结果的语法结构
*/
b++; // 3
delete a.name; // true
greeting = 'hello ' + c; // "hello abc"
const cs = Math.cos(0); // 1
'abc'; // "abc"
e(); // 3
null == undefined; // true
var status = (age >= 18) ? "adult" : "minor"; // undefined
(b >= 2) ? "adult" : "minor"; // adult
typeof "abc"
/fasf/g // /fasf/g

/*
* 空语句
*/
；
if (condition);
var i = 0;
for (; i < 9; i++) {
    console.log(i);
    // more statements
}

/*
* debugger
* 当 debugger 被调用时, 执行暂停在 debugger 语句的位置。就像在脚本源代码中的断点一样
*/
debugger;

/*
* with
* 扩展一个语句的作用域链
* 官方不推荐使用，使用过度对性能不好、易混淆导致错误、存在兼容性问题
*/
function f(foo, values) {
    with (foo) {
        console.log(foo.values); // es6 Array.prototype.value
    }
}
f([1,2,3], {});

```

### 语句块
What: 语句块就是拿大括号括起来的一组语句，它是一种语句的复合结构，可以嵌套。
How: 语句块内部的语句的`Completion Record`的`[[type]]`如果不为normal，会打断语句块后续部分的执行。
```
// eg1
{
  var i = 1; // normal, empty, empty
  i ++; // normal, 1, empty
  console.log(i) //normal, undefined, empty
} // normal, undefined, empty
console.log(i);

// eg2
{
  let i = 1; // normal, empty, empty
  i ++; // normal, 1, empty
  console.log(i) //normal, undefined, empty
} // normal, undefined, empty
console.log(i);

// eg3
{
  let i = 1; // normal, empty, empty
  i ++; // normal, 1, empty
  console.log(i) //normal, undefined, empty
} // normal, undefined, empty
console.log(i);

// eg4
const a = () => {
  var i = 1; // normal, empty, empty
  return i; // return, 1, empty
  i ++; 
  console.log(i)
}; 
a(); // return, 1, empty

```

### 控制型语句

What: 带有特定关键字，会对不同类型的`Completion Record`产生反应。控制型语句大致分为两类：

- 对其内部造成影响，如`if, switch, while/for, try/catch/finally`；
- 对其外部造成影响，如`break, continue, return, throw`；

How: <u>两类控制语句配合使用</u>，达到控制代码执行顺序和执行逻辑的效果。

||break|continue|return|throw|
|:--:|:--:|:--:|:--:|:--:|
|if|报错|报错|报错|报错|
|switch||报错|报错|报错|
|for/while|||||
|function|报错|报错|||
|try|报错|报错|||
|catch|报错|报错|||
|finally|报错|报错|||
```javascript
/*
* 穿透
*/
for(let i=0; i<2; i++) {
    console.log('i: ', i);
    for(let j=0; i<5; j++) {
        if(j === 3) break;
        console.log('j: ', j);
    }
}

for(let i=0; i<2; i++) {
    console.log('i: ', i);
    for(let j=0; j<5; j++) {
        if(j === 3) continue;
        console.log('j: ', j);
    }
}

const func = () => {
    for(let i=0; i<2; i++) {
        console.log('i: ', i);
        for(let j=0; j<5; j++) {
            if(j === 3) return j;
            console.log('j: ', j);
        }
    }
};
func();

const func = () => {
    for(let i=0; i<2; i++) {
        console.log('i: ', i);
        for(let j=0; j<5; j++) {
            if(j === 3) return j;
            console.log('j: ', j);
        }
    }
};
try {
    func();
} catch(e) {
    console.log(e);
}

if (1) {
    console.log('continue');
    continue;
    console.log('finished');
}

const func = () => {
    console.log('start');
    break;
    console.log('end');
};
func(); // error

const func = () => {
    throw 1;
};
try {
	func();
} catch(e) { console.log(e); }


/*
* 消费
*/
const func = (action) => {
    switch(action){
        case 1:
            console.log(1);
            break;
        case 2:
            console.log(2);
            continue; // error
        case 3:
            console.log(3);
            return;
        case 4:
            console.log(4);
            throw 4;
       	default:
            console.log('default');
            break;
	}
    console.log('finished');
};
const test = (n) => {
   try {
       const res = func(n);
       console.log('res ', res);
   } catch(e) {
       console.log(e);
   } 
};

for (let i=0; i<5; i++) {
    switch(i){
        case 3:
            console.log('continue');
            continue;
        default:
            break;
    }
    console.log(i);
}


/*
* try/catch/finally
* break/continue/return/throw
*/
const foo = () => {
  try {
    let a = 1;
    a++;
    throw '我错了';
    return 'try';
    // return a.sort();
  } catch(e) {
		console.log(e);
  } finally {
      	    break;
		return 'finally';
  }
};
foo();
```

### 带标签的语句

What: 任何JS语句都是可以加标签的，在语句前面加冒号即可。`label: statement`
How: 标签与`Completion Record`的`[[target]]`相配合，用于跳出多层循环。

```javascript
// eg1
first: var a = 1;

/*
* break/continue后跟关键字，会产生带target的Completion Record；
*/
// eg2
outer: while(true) {
  inner: while(true) {
      break outer;
  }
}
console.log("finished")

// eg3
var i, j;
loop1:
for (i = 0; i < 3; i++) {
   loop2:
   for (j = 0; j < 3; j++) {
      if (i == 1 && j == 1) {
         continue loop1;
      }
      console.log("i = " + i + ", j = " + j);
   }
}
```

## 参考

任务与事件循环相关参考：

<https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/>

<http://vimeo.com/96425312>

https://segmentfault.com/q/1010000011914016

<https://tc39.github.io/ecma262>