---
title: HTML换行间隙
date: 2017-09-25
categories:
- CSS
---
## 前言
在网页设计与布局时，总是会发现元素间有莫名其妙的间隙问题：明明没有设置margin或padding或position，但就是有条大约3px的缝。

## 原理
若出现连续几个内联标签（inline-block）或表单元素标签的换行在浏览器会被解释为一个空格。

## 实践
``` bash
<!-- 此例有缝隙 -->
<style>
    input{width: 120px; height: 30px;}
</style>
<body>
    <input type="text" value="空格">
    <input type="text" value="测试">
</body>
```
**解决办法**：
- 写html代码时不要换行，直接连着写。
- 将其父级元素字体大小font-size设为0。
- 将要除间隙的元素的margin设置为-3。

## 小结
此问题属于代码编写的日常问题，前端代码很多这种类似的日常坑，得多注意。这样利于代码的规范化。