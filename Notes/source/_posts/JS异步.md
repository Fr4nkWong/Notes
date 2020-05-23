---
x1title: ES6异步&迭代器
date: 2017-12-13
category:
- javascript
---
## 前言
javascript给我的感觉比较直观，从上到下一条路走完就ok。对于异步，js一直以来的实现方式是回调，最典型的应用场景就是事件响应了。不过简单逻辑下回调易读且清晰明了，然而在复杂逻辑下就会表现出层层嵌套的复杂且不易读的横向效果(俗称“回调地狱”)。ES6发布`Promise`和`Generator函数`就是为了简化这一效果使代码呈现纵向且按“同步+断点“的方式呈现，而ES8更是引入了`async/await`以简化异步(其底层还是封装的Generator)。

## Iterator
- **Iterator(遍历器)是一种接口规格，任何对象只要部署这个接口，就可以完成遍历操作，就可以称为遍历器。**它的作用有两个：一是为各种数据结构，提供一个统一的、简便的接口；二是使得对象的属性能够按某种次序排列。默认情况下，数组、类数组对象、Set、Map原生部署该接口，而其他对象的Iterator接口可自行部署(原型链上的对象具有该方法即可，该方法部署在一个键名为Symbol.iterator的属性上，该函数返回一个遍历器对象)。
- for..of :遍历遍历器的表达式，这里一定要和for..in区分开。for...of循环就是内部调用了原对象的Iterator接口方法，所以能用这个循环的对象都是部署了该接口的即遍历器。
- iterator.next(): 遍历器提供了一个指针，指向当前对象的某个属性，使用next方法，就可以将指针移动到下一个属性。该方法返回一个对象 {value:'yield语句的值',done:'true'} ，done表示遍历是否结束。Iterator接口返回的遍历器，原生具备next方法，不用自己部署(DIY另说)。**遍历器只有调用next()才会遍历下一个成员。**这一点对于后面理解Generator函数和yield命令的理解有帮助。

``` bash
let o = {
	name: 'frank',
	age: '22'
};
//for...in 可遍历键名
for(e in o){
	console.log(e);
	//name
	//age
}
//该对象没有Iterable接口
for(e of o){
	console.log(e);
	//TypeError: o is not iterable
}
//原生支持Iterator接口，例如Array
let arr = [1,2];
let iterator = arr[Symbol.iterator]();
iterator.next(); //{ value: 1, done: false }
iterator.next(); //{ value: 2, done: false }
iterator.next(); //{ value: undefined, done: true }

//为对象diy Iterator接口
let obj = {
  data: [ 'hello', 'world' ],
  [Symbol.iterator]() {
    const self = this;
    let index = 0;
    return {
      next() {
        if (index < self.data.length) {
          return {
            value: self.data[index++],
            done: false
          };
        } else {
          return { value: undefined, done: true };
        }
      }
    };
  }
};
iterator = obj[Symbol.iterator]();
iterator.next()  //{ value: "hello", done: false }
iterator.next()  //{ value: "world", done: false }
iterator.next()  //{ value: undefined, done: true }
//Generator函数实现Iterator接口
let obj = {
  * [Symbol.iterator]() {
    yield 'hello';
    yield 'world';
  }
};
```

## Generator函数
- Generator(生产者): ES6引入Generator函数，作用就是可以完全控制函数的内部状态的变化且依次遍历这些状态(即函数内部状态遍历器)，每调用一次就会使函数内部状态发生一次改变。只要是 function* 的函数都是generator函数，只有generator函数里才能使用yield。**调用generator函数会返回一个可遍历对象(遍历器)。**
- yield(产出): generator函数体内部使用yield语句，**定义遍历器的每个成员，即不同的内部状态**。Generator函数返回的遍历器触发next()时，从Generator函数起点或上一个yield位置运行到之后遇到的第一个yield位置，弹出Generator函数并以此yield后的表达式的值作为返回对象的value属性值。也就是说**yield是Generator函数的充分不必要条件，无论Generator函数里有无yield语句都只能通过next()触发函数执行。**
- generator.next(): yield语句本身没有返回值(返回undefined)，能返回的是紧随其后的表达式的值。next方法可以带一个参数，该参数就会被当作**上一个yield语句后表达式的值**并继续Generator函数里的执行。
- for...of : 可以自动遍历Generator函数且此时不再需要next()，return值不会包含在遍历结果中。
- throw() : 可以在Generator函数体外抛出错误，然后在函数体内捕获。
- yield* : **表示后面跟的是一个遍历器**。可以延伸出使用yield* 的基本套路：yield* + 原生部署Iterator接口的数据结构; yield* + Generator函数; yield* + diy了\[Symbol.iterator\]()的Object 。

``` bash
//yield基本使用例子
function* generator(){
	yield 1;
	yield 2;
	return 3;
}
var g = generator(); //generator函数返回一个遍历器对象
g.next()  //{ value: 1, done: false }
g.next()  //{ value: 2, done: false }
g.next()  //{ value: 3, done: true }
g.next()  //{ value: undefined, done: true }
//next(x)用法
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}
var it = foo(5);
it.next(); //{ value:6, done:false }
it.next(12); //{ value:8, done:false }
it.next(13); //{ value:42, done:true }
//for...of不遍历return
function* func(){
	yield 1;
	yield 2;
	return 3;
}
for(let v of func()){
	console.log(v);
	//1 2
}
//yield*基本用法
let obj = {
	//ES5写法
  generatorMethod2:function* {}，
	//ES6写法
	* generatorMethod1() {}
};
function* gen(){
  yield* ["a", "b", "c"];
}
gen().next() //{value:"a", done:false }
```

## Promise

Promise是什么？`console.dir(Promise);`

- `const promise = new Promise(resolve,reject)`：简单说就是一个容器，里面保存着某个未来才会结束的事件的结果(所携带的2个函参用于状态变更回调)。Promise对象有3个状态：
   - `pendding`：待定状态，Promise对象刚被初始化
   - `fullfilled`：完成状态，承诺完成后
   - `rejected`：失败状态，承诺完成失败
   - **状态变更具有不可逆性！**：①->②；①->③；
- `Promise.resolve()`：调用后Promise对象状态从`pending`到`fullfilled`。如果`resolve()`的参数，不是具有then方法的对象（又称thenable对象），则返回一个新的Promise对象，且它的状态为fulfilled。用于将现有对象转为Promise对象，实际效果根据参数而定：
   - Promise实例
   - thenable对象
   - 不具有then方法的对象
   - 不带任何参数
- `Promise.reject()`：调用后Promise对象状态从`pending`到`rejected`。`reject()`返回的Promise实例的状态为`rejected`，参数和回调处理与`resolve()`相同。
- `Promise.all([promise1, ...])`： **参数必须部署Iterator接口**，返回一个Promise实例；该方法用于将多个<u>Promise实例</u>，包装成一个新的Promise实例。本质上是**非抢占式**，promise并行执行，它们都执行完后才会一起进入`then([res1, ...])`；
- `Promise.race([promise1, ...])`：**参数必须部署Iterator接口**，返回一个Promise实例；该方法用于将多个Promise实例，包装成一个新的Promise实例。本质上是**抢占式**获取then()的使用权，剩下的实例执行完后便停止。
- `Promise.prototype.then(res, err)`：链式调用；then方法中的参数是`Promise.resolve()`传递的参数或者是上一个`then()`的返回值。
- `Promise.prototype.catch(err)`: 等同于`Promise.prototype.then(null,err) `；用于指定发生错误时的回调函数，异步回调中捕获异常中起到了`try{...}catch{...}`的作用。Promise的错误具有沿链向后传递的性质，错误会一直传递直到被捕获，所以**catch()可以捕获到前面所有then方法中的错误**。
- `Promise.prototype.finally()`：ES9引入标准，不管Promise对象最后状态如何都会执行，参考`try{}catch{}finally{}`。

``` javascript
//写法一
const promise = new Promise(function(resolve,reject){
	if(//success){
		resolve();
	}else{
		//failure
		reject();
	}
});
//写法二
(new Promise()).then(function(){
	//等同resolve()
	return x;
},function(){
	//等同reject()
}).then(function(x){
	//x上一个函数的返回值
}).catch(function(err){});
//Promise.all()
promise = Promise.all([p1,p2,p3]);
let p = Promise.all('frank');
p.then(function(x){{
//因为p处于fullfilled，立即输出'frank'	
	console.log(x);
}});
//aync函数例子
async function a(x){
	await returnPromise(x);
	return x;
}
```

## async/await

**0x01: async函数**：只要函数名前有`async`关键字，就表明该函数是async异步函数。

**0x02: await**：只能在async函数中使用`await`关键字，表明此处阻塞，直到其后跟的Promise执行完。

- 从async函数外部看，
  - async函数返回一个Promise对象；
  - async函数类似执行`Promise.all()`，其内部所有await后的Promise对象都执行完后，函数返回的Promise对象的状态才会发生改变(除非遇到return或抛出错误)；
- 从async函数内部看，
  - async函数`return`语句后的返回值会成为async函数返回的Promise对象在调用then()时的参数；
  - await后跟的若是`Thenable`对象，返回该对象的结果；若后跟的不是Promise对象，就直接返回对应的值；
  - 任何一个`await`语句后面的 Promise 对象变为`reject`状态，那么整个`async`函数都会中断执行，且reject()的参数会被catch()的回调函数接受；

```javascript
// eg1: await + thenable object
class Sleep {
  constructor(timeout) {
    this.timeout = timeout;
  }
  then(resolve, reject) {
    const startTime = Date.now();
    setTimeout(
      () => resolve(Date.now() - startTime),
      this.timeout
    );
  }
}

(async () => {
  const actualTime = await new Sleep(1000);
  console.log(actualTime);
})();

// eg2
async function f() {
  await Promise.reject('出错了');
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))// 出错了

```

## 小结
或许是我深受python yield和iterator荼毒...第一次看的时候觉得自己见过的很简单，但忽略了细节比如迭代的本质是实例的原型链上有无[x1[Symbol.iterator]]。在彻底搞懂Iterator后再看Generator容易懂很多，而async/await无非是在Promise的基础上加了层语法糖。但是延伸一下思维的边界，若把箭头函数、Generator、Promise组合在一起是可以把代码变得很简洁很geek的，这里的细节光想想就不少～爬坑从现在开始2333