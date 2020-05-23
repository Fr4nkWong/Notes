---
title: 浮动&清除浮动&BFC
date: 2017-09-06
categories:
	- CSS
---
## 什么是浮动
浮动属性会让拥有它的元素脱离文档流，但元素内的文本内容还是会保证全部显示(至于是否如正常情况一般显示就不一定了)。浮动常见的使用场景可能就是实现文字环绕的特效，例子如下：
<img src="./img/浮动3.jpg" />
正常情况下，在未给元素设置浮动时，元素排列方式排列：
<img src="./img/浮动1.png" />
给left块设置float:left后会发现元素排列情况如下：
<img src="./img/浮动2.png" />
以上效果出现原因：left块因为左浮动，而使得原先元素在文档流中占有的位置被删除；当right块补上原先的位置时，right块中的字体被挤出来。
float是被用来布局的属性。现在float一般是用于触发BFC以确保元素的宽度不再自适应父元素宽度，而是适应自身内容。这样就可以，轻松地实现多栏布局的效果。

## 清除浮动方法
- 父级元素设置伪类元素：:after{clear:both;}
``` bash
<div class="outer"> //这里添加了一个class
 <div class="div1">1</div>
 <div class="div2">2</div>
 <div class="div3">3</div>
</div>

.outer:after{
 /*==for IE6/7 Maxthon2==*/
 .outer {zoom:1;}    
 /*==for FF/chrome/opera/IE8==*/
 .outer :after {clear:both;content:'.';display:block;width: 0;height: 0;visibility:hidden;}   
}
```

- 父级(浮动元素们肯定是被包含在一个块级元素里的)元素设置样式：overflow: hidden; zoom: 1; //zoom: 1; 是在处理兼容性问题
``` bash
<div class="outer over-flow"> //这里添加了一个class
 <div class="div1">1</div>
 <div class="div2">2</div>
 <div class="div3">3</div>
</div>

.over-flow{
    overflow: hidden; zoom: 1; //zoom: 1; 是在处理兼容性问题
}
```

## BFC(Block Formatting context)
译为块级格式化上下文。它是一个独立的渲染区域，只有Block-level box(参考盒模型)参与， 规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。

- BFC特点：
  -  BFC域内部box会在垂直方向，依次放置。
  -  BFC域内部box垂直方向距离取决于margin，且同BFC域下的相邻box的margin会重叠。
  -  BFC域不会与浮动盒子重叠。
  -  BFC域是一个独立的容器，域内子元素无法影响BFC域外的元素。反之亦然。
  -  BFC域内的浮动元素纳入BFC域高度的计算。

- 产生BFC的条件
	- 根元素
	- float属性不为none
	- position为absolute或fixed
	- display为inline-block, table-cell, table-caption, flex, inline-flex
	- overflow不为visible

向大佬学习：https://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html

## 小结
这三种方法是目前我能想到的清除浮动最简单的方法了，以后有好的想法会继续在此基础上补充。