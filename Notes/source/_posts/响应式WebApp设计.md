---
title: 响应式WebApp设计
date: 2017-09-01
categories:
	- WEB
---
## 前言
PC、Phone、Pad三端适配使用已经是大趋势，核心难点在于如何适配主要的三大设备正常显示设计稿。而设计稿的复现在于合理布局和动态适配的尺寸。做设备响应式适配的第一功能需求就是**随着分辨率的变化，页面元素的尺寸和间距都相应变化**。

## 动态适配尺寸
- 尺寸单位：px, em, rem 。
我们用px写绝对单位，em写相对父元素的单位，rem写相对html根元素的单位。
- 物理像素(physical pixel)：一个物理像素是显示器(手机屏幕)上最小的物理显示单元。在操作系统的调度下，每一个设备像素都有自己的颜色值和亮度值。
- 设备独立像素(density-independent pixel/device-width)：设备独立像素(也叫密度无关像素)，可以认为是计算机坐标系统中得一个点，这个点代表一个可以由程序使用的虚拟像素(比如: css像素px)，然后由相关系统转换为物理像素。
- 设备像素比(device pixel ratio )，DPR定义了物理像素和设备独立像素的对应关系：
**设备像素比 * 缩放比例 = 物理像素(设备物理分辨率)/设备独立像素** //在某一方向上，x方向或者y方向
- **得确定设计稿的横向分辨率并求出rem值，然后根据公式这个适配其他设备。**

``` bash
//HTML配置设备适配
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
//JS获取到当前设备的dpr
window.devicePixelRatio; 
//进行媒体查询，对不同dpr的设备，做一些样式适配(这里只针对webkit内核的浏览器和webview)
-webkit-device-pixel-ratio，-webkit-min-device-pixel-ratio, -webkit-max-device-pixel-ratio
//计算公式
dpr * scale = physical pixel/device-width
//默认html标签的font-size为16px
1rem = 16px;
device-width = rem * font-size
```

## 适配布局
关于适配布局的第一选择当属FLEX布局了，至少在我看来PAD和PHONE端是不用愁了(PC可能还需要改改，毕竟不能一直拉伸到PC屏幕宽度，看着好怪的...)。当然了，也可以引用第三方库来节省麻烦，比如flexible.js:
``` bash
<script src="http://g.tbcdn.cn/mtb/lib-flexible/0.3.4/??flexible_css.js,flexible.js"></script>
```
这个库执行后会根据不同的设备在元素上添加一个data-dpr属性并设置根元素font-size样式。

## 实现思路
实现适配最简单粗暴的就是针对不同的情况写媒体查询CSS而且是条件越具体越好，但是使用rem布局结合根据不同分辨率设置不同的根元素font-size有很多不好解决的麻烦：工作量和复杂度较高、设计稿都是以分辨率来标明尺寸的，根据设计稿里各个元素的像素尺寸转换为rem时，该以哪个font-size为准呢？为了提高代码的可重用性，可以用CSS预处理器进行封装。
0x01:
页面上html的font-size不用预先通过媒介查询在css里定义好，而是通过js计算出来(所以font-size是在html元素标签里的)。将设计稿的横向分辨率除以设计稿的根元素font-size得到rem。当分辨率发生变化时，html的font-size就会变(不过这得在调整分辨率后刷新页面才能看得到效果)，剩下的尺寸都用rem。
``` bash
<meta name="viewport" content="initial-scale=1,maximum-scale=1, minimum-scale=1">
//after dom ready, get device-width
document.documentElement.style.fontSize = document.documentElement.clientWidth/7.5 + 'px';
//7.5为横向分别率
如果设计稿基于iphone6，横向分辨率为750，html font-size:100px，body的width为750/100 = 7.5rem
```
<img src="/img/适配布局1.png" />

0x02:
通过动态配置viewport的scale，让网页因设备不同的devicePixelRatio而自适应改变scale以此达到让整个网页在设备内显示时的页面宽度等于device-width的目的，此方法与CSS预处理器配合使用效果较好。设计稿在设计时就将设计稿device-width/font-size = 10 ，font-size = 10px，font-size可能需要额外的媒介查询，并且font-size不使用rem，
``` bash
//动态配置适配
var scale = 1/dpr;
document.querySelector('meta[name="viewport"]').setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
document.documentElement.style.fontSize = document.documentElement.clientWidth/10 + 'px';
//配合LESS，假设iphone6情况下
//定义一个变量和一个mixin
@baseFontSize: 75; //(font-size = deviceWidth/10)
.px2rem(@name, @px){
  @{name}: (@px/@baseFontSize)*1rem;
}
//使用示例：
.container {
  .px2rem(height, 240);
}
//less翻译结果：
.container {
  height: 3.2rem;
}
```

## 小结
做适配目的要明确：
- 都能适配所有的手机设备，对于pad，大可跳转到pc页面，不再使用触屏版的页面。
- 都需要动态设置html的font-size。
- 布局时各元素的尺寸值都是根据设计稿标注的尺寸计算出来，由于html的font-size是动态调整的，所以能够做到不同分辨率下页面布局呈现等比变化。

从网上截取分析并总结了这2个大致的思路，想要完美实现还需要在很多小细节下功夫，适配属于工程化的一个必修课程，之后有好的想法或实践时出的坑我也会进一步更新。
