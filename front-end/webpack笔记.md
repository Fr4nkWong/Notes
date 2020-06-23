---
title: webpack笔记
date: 2019-03-20
categories:
- front-end
---

# What
将前端项目代码根据模块间引用关系按指定规则关系进行打包的工具。
# Why
日益庞大且复杂的前端项目需要一个具备一定体系的工具去编译、打包、调试、管理代码。
简单脚本处理 => CommonJS => AMD(require.js) => grunt/gulp => SPA => webpack

# How
- 需要安装全局webpack脚手架，`webpack-cli`是命令行工具，支持了命令执行`webpack.config.js`配置实现打包；
  `npm i -g webpack-cli`
- webpack是核心库，主要负责编译打包；
  `npm i --save-dev webpack webpack-cli`
- webpack为开发者提供调试的服务器，当然NGINX也不错但没这么方便；
  `npm install webpack-serve --save-dev`
  `webpack-serve webpack.config.js`
- `package.json`配置命令；
```
script: {
	"dev": "webpack webpack.config.js",
	"build": "webpack webpack.config.productuon.js"
}
```
## 输入输出
- 单入口/单出口
- 单入口/多出口
- 多入口/单出口
- 多入口/多出口
## 文件处理
### js

`babel-loader`写法与`.babelrc`类似；

```
// module.rules: []
{
    test: /\.jsx?$/,
    use: [
        exclude: /(node_modules)/,
        loader: 'babel-loader',
        options: {
            presets: [],
            plugins: []
        }
    ]
}
```
### css

`mini-css-extract-plugin`

```
// module.rules: []
{
    test: /\.(less|css)$/,
    use: [{
        loader: MiniCssExtractPlugin.loader,
        options: {
            hmr: process.env.NODE_ENV === 'development'
        }
    },
    {
    	loader: 'css-loader',
        options: {
            importLoaders: 1
        }
    }, 
    {
    	loader: 'postcss-loader',
        options: {
          sourceMap: true,
          plugins: (loader) => [
            require('autoprefixer')({
              browsers: ['Android >= 4', 'iOS >= 7']
            })
          ]
        }
    },
    {
    	loader: 'less-loader',
        options: {
            sourceMap: true,
            javascriptEnabled: true
        }
    }]
}
```
### html

`html-webpack-plugin`

### img

`file-loader`

### 静态资源路径
## Babel编译

`npm i -D babel-core babel-cli babel-preset-env babel-register babel-polyfill babel-plugin-transform-runtime babel-loader babel-preset-react `

- `babel-loader`

- `.babelrc`
```
{
	"presets": [
		[
			"@babel/env",
			{
				"useBuiltIns": "entry",
				"targets": {
					"node": 10,
					"chrome": 62,
				},
				"loose": true,
				"modules": "commonjs"
			}
		]
	],
	"plugins": [
		"@babel/transform-runtime"
	]
}
```
## 代码分离
分离业务代码和第三方库
## 兼容性
## 性能
### 增量编译
### 异步加载文件
`async/await`+`dynamic-import`：

`npm i -D babel`，配置`.babelrc`
### 多线程打包
happypack

## 调试模式

`npm i -D webpack-serve`

`webpack-serve webpack.config.js`

# 爬坑记录
0x01：webpack4开始已经弃用`extract-text-webpack-plugin`插件，改为支持`mini-css-extract-plugin`插件
0x02：`NormalModuleReplacementPlugin`有待改进；
0x03：解析`import`动态调用，`.babelrc`里需要配置`babel-plugin-syntax-dynamic-import`；`webpack.config.module`里对应文件需要用`dynamic-import-webpack`；

