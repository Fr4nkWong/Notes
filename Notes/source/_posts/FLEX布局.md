---
title: FLEX布局
date: 2017-07-14
categories:
	- front-end
tags:
  - css
---
## 前言
目前网页布局有4种：静态布局、自适应布局、流式布局、响应式布局。得益于智能移动端市场的崛起和HTML5、CSS3的广泛使用，响应式布局变得非常的流行。布局的传统解决方案基于盒状模型，依赖 display 属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。响应式布局可以看作是流式布局和自适应布局设计理念的融合，而响应式布局除了媒体查询外的主要CSS核心便是弹性盒子布局了。
## 基本概念
flex译为“弹性布局”，为传统盒子模型提供一定的灵活度、弹性。注意，设为 Flex 布局以后，**子元素**的float、clear和vertical-align属性将**失效**。
``` bash
.container{
	display:flex | inline-flex;
	display:-webkit-flex; /*webkit内核浏览器须加前缀*/
}
```
采用flex布局的元素，称为flex容器。其子元素称为容器成员，又译为flex项目。
## 容器属性
- flex-direction: 决定主轴的方向（即项目的排列方向）。
``` bash
.container{
/*从左到右、从右到左、从上到下、从下到上*/
  flex-direction: row | row-reverse | column | column-reverse;
}
```
- flex-wrap: 定义如果一条轴线排不下时的换行方式。
``` bash
.container{
/*(默认)不换行、第一行在上、第一行在下*/
	flex-wrap: nowrap | wrap | wrap-reverse;
}
```
- **flex-flow**: flex-direction和flex-wrap的简写形式，默认值为row nowrap。
``` bash
.container{
	flex-flow: <flex-direction> || <flex-wrap>;
}
```
- justify-content: 定义了项目在主轴上的对齐方式。
``` bash
.container{
/*(默认)左对齐、右对齐、居中、两端对齐，项目间隔相等、每个项目两侧间隔相等*/
	justify-content: flex-start | flex-end | center | space-between | space-around;
}
```
- align-items: 定义项目在交叉轴上如何对齐。默认为stretch，如果项目未设置height或设置为auto，将占满整个容器高度。
``` bash
.container{
/*交叉轴起点对齐、交叉轴终点对齐、交叉轴中点对齐、项目第一行文字的基线对齐*/
	align-items: flex-start | flex-end | center | baseline | stretch;
}
```
- align-content: 定义了多根轴线的对齐方式。**如果项目只有一根轴线，该属性不起作用。**默认stretch，轴线占满整个交叉轴。
``` bash
.container{
/*与交叉轴起点对齐、与交叉轴终点对齐、与交叉轴中点对齐、与交叉轴两端对齐，轴线之间间隔平均分布、每根轴线两侧间隔相等。*/
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
## 项目属性
- order: 定义项目的排列顺序。数值越小，排列越靠前，默认为0。
``` bash
.item{
  order: <integer>;
}
```
- flex-grow: 属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
``` bash
.item {
 flex-grow: <number>; /* default 0 */
}
```
- flex-shrink: 定义了项目的缩小比例。默认为1，即如果空间不足，该项目将缩小。
``` bash
.item{
	flex-shrink: <number>;
}
```
- flex-basis: 定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
``` bash
.item{
	flex-basis: <length> | auto;
}
```
- **flex**: flex-grow, flex-shrink 和 flex-basis的简写。默认值为0 1 auto，后两个属性可选。**建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。**
``` bash
flex: none | [<flex-grow> <flex-shrink>? || <flex-basis>];
```
- align-self：**允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性**。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
``` bash
.item{
	align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
## 小结
从接触前端到现在也差不多1年了，目前在学习node.js和vue.js。同时在对一个以前的项目用es6+html5+css3+node+mysql+express进行重构，在做手机适配时遇到了好多坑，发现自己对真正的响应式布局其实并不了解后恶补填坑...布局、定位、2D变形、3D动画、渐变、过渡、选择器都是CSS3里的重中之重，才发现自己都不敢肯定地点头说自己掌握诶。革命尚未成功，同志仍须努力啊~不多说了，继续填坑ing...