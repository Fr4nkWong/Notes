---
title: JS模块
date: 2017-12-18
categories:
	- javascript
---
## 前言
随着Node.js的崛起，javascript不再是当初只能用于浏览器端事件和视觉操作的脚本语言。它可用于在PC端编译运行且可搭建后端，现在我认为几乎在走Python的路子，Python有个pip而Node.js有个npm，同理代码模块化竟然也是学了过来。

## Class
Class: ES6引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。类和模块的内部，默认就是严格模式，故无需使用`'use strict'`。
extends: 该关键字用于继承，类似java。
constructor: 构造函数。
super: super指代父类对应方法。
this: 代表实例对象。

```
//ES5写法
function Person(x,y){
	this.name = x;
	this.y = y;
}
Person.prototype.toString = function(){
	return 'this is '+this.name;
}
//ES6写法
class Person extends Animal{
	constructor(x,y) {
		//super.constructor(x)
		super(x); 
		this.name = y;
	}
	toString() {
		//super调用父类toString()
		return `this is ${this.name}`+super();
	}
}
```

## Module & import/export
ES6之前javascript是没有模块体系标准的，都是有社区制定了一些模块加载标准，主要有CommonJS(服务器)和AMD(浏览器)。CommonJS和AMD模块，都只能在运行时确定这些东西，而ES6模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。所以ES6可以在编译时就完成模块编译，效率要比CommonJS模块高。**ES6模块不是对象，而是通过export命令显式指定输出的代码，输入时也采用静态命令的形式。**
- export: 用于用户自定义模块，规定对外接口。在一个文件或模块中，export、import可以有多个。export可以输出变量、方法、类。export{}必须写在脚本尾部。
- export default: 用于指定模块的默认输出。**如果模块加载时，只能输出一个值或方法或类，那就是export default所指定的那个值或方法。。一个模块只能有一个默认输出，因此export deault命令只能使用一次。**所以，import命令后面才不用加大括号。
- import: 用于输入其他模块提供的功能，同时创造命名空间(namespace)，防止函数名冲突。在输入中，{}变量名得与export中{}一致且位置对应，当然也可以用as在引用文件里对变量重命名。
- 模块继承：

``` bash
/*info.js*/
//export1
export let name = 'frank';
export let age = '22';
export function hello(){
	console.log('hello');
}

//export2
let name = 'frank';
let age = '22';
function hello(){
	console.log('hello');
}
export {name as x, age, hello};

//export3
export default {
	name: 'frank',
	age: '21',
	hello(): {
		console.log('hello');
	}
}

/*main.js*/
//export1,2 -> import1
import {x as name, age, hello} from './info';

//export1,2,3 -> import2
import * as person from 'info';
//等价于
module person from 'info';
person.hello(); //'hello'

//export3 -> import3
import person from './info'
import $ from 'jquery';
person.hello(); //'hello'
```

## require/exports
对于require/exports和import/export得分清楚，只看表面都是模块化引用但往深了说还是有不小的区别。关于具体细节区别可进传送门：https://www.zhihu.com/question/56820346/answer/150743994
目前Node基于的CommonJS还未兼容ES6规范，所以真相是目前require/exports 是必要且必须的，因为目前你编写的 import/export 最终都是编译为 require/exports 来执行的。

1.require和import的主要区别：
- 规范：require/exports以ComonJS(Node基于此)为标准；import/export以ECMAScript为标准。
- 加载方式：require是动态加载；import是静态加载且只能置于脚本头部。
- 特点：require/exports是社区的方案，提供了服务器/浏览器的模块加载方案，它是非语言层面的标准，**只能在运行时**确定模块的依赖关系及输入/输出变量，无法进行静态优化；import/export是语言规格层面支持模块功能，支持编译时静态分析，便于JS引入宏和类型检测，它是**动态绑定**的。

2.说了那么多，回归到实践，接下来讲讲require/exports的东西（官文：https://nodejs.org/api/modules.html ）：
- Node中，模块分为核心模块和文件模块。核心模块是被编译成二进制代码，引用的时候只需require表示符即可，如：require('net')。文件模块则是指js文件、json文件或者是.node文件。在引用文件模块的时候后要加上文件的路径，若不加路径则该模块要么是核心模块要么是该项目下node_modules目录里的模块。具体require搜索顺序见下面例子。

``` bash
/* /Users/frank/a.js */
var obj = {
	name: 'frank',
	age: 21
};
module.exports = obj;

/* /Users/frank/b.js */
var person = require('./a');
var net = require('net');
/*
/Users/frank/node_modules/a.js
/Users/node_modules/a.js
/node_modules/a.js
*/
console.log(person.name);
```

## 小结
本文的ES6特性都还未得到CommonJS的兼容，所以Node目前都使用不了(本质上)的，当然可以引入babel进行转码，ES6是根正苗红的标准所以我们完全有信心和理由相信ES6就是未来，我需要的只是静静等待。现在很多coder写ES6代码是因为很高逼格还有便于以后的维护，毕竟未来是ES6的，到时候维护升级代码太麻烦。