---
title: babel笔记
date: 2019-05-10
categories:
- front-end
---

## 前言

babel与JS的历史发展有关，首先了解以下5个概念：

- 标准`Standard`： 用于定义与其他事物区别的一套规则。
- 实现`Implementation`： 某个标准的具体实施/真实实践。

- `ECMAScript`：JS的标准；最终能否使用标准还得靠各大浏览器厂商的实现去支持；
- `JavaScript`：JS的实现；在各大浏览器内核中的JS引擎实现代码的解释和执行，特性受浏览器厂商具体实现的限制；
- `Node.js`：基于V8引擎在服务端执行的JS，代码会被怎么解释最终还是由V8说了算；

在积极拥抱JS的未来、使用JS新的特性的同时，为了能兼容还未完全支持ECMA新特性的浏览器，我们需要一个工具将我们充满ECMA新特性的源码转译成为浏览器目前支持的JS版本，所以babel诞生了。

## Babel

对于ECMAScript新的特性，大致可以从2个方面总结：

- syntax：babel只负责这部分的转码；
- API：`@babel/polyfill`可以提供；

### .babelrc

What：babel的配置文件，存放在项目的根目录下，用来设置转译规则和插件。

How：执行babel命令，babel会根据其根目录下的`.babelrc`里写的进行转译。

- **presets**
  - what：作为插件的组合而存在，并在配置里在`presets`里引用。
- **plugins**
  - what：负责实现具体某个ECMA特性。

```
npx babel src/ --out-dir app/

// .babelrc
{
	"presets": [ // 预设 = n * 插件
		"@babel/preset-env",
		"@babel/preset-react"
	],
	"plugins": [ // 插件
		"@babel/transform-runtime"
	],
	"env": { // 根据环境使用插件
		"development": {
			plugins: []
		},
		"production": {
			plugins: []
		}
	}
}
```

### @babel/core
What：提供代码层面的接口供开发者使用，多用于自写`preset`和`plugin`。

How：以编程的方式使用babel。

```
npm i --save-dev @babel/core

import babel from '@babel/core';

babel.transform();
babel.transformFile();
babel.transformFileSync();
```

### @babel/cli
What：提供命令行下运行babel的功能。内含`@babel/node`库。

How：命令行命令触发，寻找命令所在目录下的`.babelrc`并依据其配置执行。

```
npm i --save-dev @babel/cli

// terminal
$ babel src/ -d app/

// package.json
{
	...
	"scripts": {
		"compile": "babel src/ -d app/"
	}
	...
}
```

### @babel/register
What：运行babel的另一种方法实时转码，所以只适合在开发环境使用。

How：只需要在项目入口或者文件头部引入文件就可以运行babel。

```
npm i --save-dev @babel/register

require('@babel/register')({
	presets: [],
	plugins: []
});
```

### @babel/polyfill

What：负责提供ECMA新的API，为当前环境提供一个垫片（babel默认只转换新的syntax，而不转换新的API）。

How：在入口处引入，即为宿主的全局环境加上新API并直接使用。

```
npm i --save-dev @babel/polyfill

// 入口处
import '@babel/polyfill';

// 主要页面引用
<script src="/static/lib/polyfill.js"></script>
```

### @babel/runtime

What：用于模拟宿主支持的环境。`@babel/runtime`对于`@babel/polyfill`的关系类似于，`.babelrc`中plugin与preset的关系。

How：

```
npm i --save-dev @babel/runtime

import _createClass from '@babel/runtime/helpers/createClass';
```

## 实践
### polyfill.io
动态按需加载浏览器不支持的JS特性：

<script src="https://polyfill.io/v3/polyfill.min.js?features=default%2CPromise"></script>

### babel-preset-airbnb

### babel-plugin-webpack-alias