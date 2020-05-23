---
title: 懒加载&预加载相关
date: 2017-08-21
categories:
	- front-end
---
## 前言
加载页面时，最影响加载速度莫过于图片了。对于用户体验来说，第一重要的莫过于加载速度了，交互流畅、页面设计之类的都是后话。有不少论坛什么的都采用了懒加载的方式加载页面就是为了提升页面加载速度。预加载和懒加载两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。

## 懒加载原理及思路
延迟加载：优先显示别的，等别的显示完了，再加载图片（优先显示其他东西）。
**核心原理：根据浏览器可视区域，加载本应该出现在区域内的图片，而不在区域内的图片不加载。**
关于懒加载最根本的初衷就是以上了，但是原理虽一样，可以有不同的思路去实现它。目前我采用的思路是：将本该放入< img \>的src里的url先不放入其中。将其放其对应的< img \>里的某个自定义属性里，比如data-url。然后监听页面滚动事件，当此图的上边界的y轴值小于此页面可视高度加上滚轮下滚的高度时(offsetTop < clientHeight + scrollTop)，将data-url里的值赋给src。当然也可以直接setTimeout()或setInterval()延迟加载。
优点：作为服务器前端的优化，减少请求数或延迟请求数。对于含有很多图片的比较长的网页来说，可以加载的更快，并且还能节省服务器带宽。

### HTML API：
 - scrollHeight: 获取对象的滚动高度。 
 - scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离 
 - scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离 
 - scrollWidth:获取对象的滚动宽度 
 - offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度 
 - offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置 
 - offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置 
 - event.clientX 相对文档的水平座标 
 - event.clientY 相对文档的垂直座标 
 - event.offsetX 相对容器的水平坐标 
 - event.offsetY 相对容器的垂直坐标 
 - document.documentElement.scrollTop 垂直方向滚动的值

### Javascript API
 - 假想你现在面对的是一个左右上下都有滚轮的页面，脑补出来后在看下面的概念：
 - document.body.clientWidth：网页可见区域宽
 - document.body.clientHeight：网页可见区域高
 - document.body.offsetWidth：网页可见区域宽(包括边线的宽) 
 - document.body.offsetHeight：网页可见区域高(包括边线的宽) 
 - document.body.scrollWidth：网页正文全文宽 
 - document.body.scrollHeight：网页正文全文高
 - document.body.scrollTop：网页被卷去的高
 - document.body.scrollLeft：网页被卷去的左
 - window.screenTop：网页正文部分上
 - window.screenLeft：网页正文部分左
 - window.screen.height：屏幕分辨率的高
 - window.screen.width：屏幕分辨率的宽
 - window.screen.availHeight：屏幕可用工作区高度
 - window.screen.availWidth：屏幕可用工作区宽度

### Element.getBoundingClientRect()
这个方法返回一个名为ClientRect的DOMRect对象，包含了top、right、botton、left、width、height这些值。返回的元素位置是相对于左上角而言的，而不是边距。这个方法有个明显的缺点：由于scroll事件密集发生，计算量很大，容易造成性能问题。下图可便于理解此方法：
<img src="./img/getBoundingClientRect.png" />

### IntersectionObserver API
- IntersectionObserver用来了解某个元素是否进入了"视口"（viewport），即用户能不能看到它。observe()的参数是一个 DOM 节点对象。如果要观察多个节点，就要多次调用这个方法。**ntersectionObserver API是异步的，不随着目标元素的滚动同步触发，这个观察器的优先级非常低，只在其他任务执行完，浏览器有了空闲才会执行。**
- callback函数的参数（entries）是一个数组，每个成员都是一个IntersectionObserverEntry对象。举例来说，如果同时有两个被观察的对象的可见性发生变化，entries数组就会有两个成员。
- IntersectionObserverEntry对象提供目标元素的信息，有6个属性：time、rootBounds、boundingClientRect、intersectionRect、intersectionRatio、target。
- IntersectionObserver构造函数的第二个参数是一个配置对象。可以设置2个属性：threshold、root和rootMargin。

``` bash
/*
目标元素的可见性变化时，就会调用观察器的回调函数callback(开始可见、开始不可见)
option是配置对象(该参数可选)
*/
var io = new IntersectionObserver(cllback, option);
// 开始观察
io.observe(document.getElementById('example'));
// 停止观察
io.unobserve(element);
// 关闭观察器
io.disconnect();
```

### lazy load demo
``` bash
//这里还是为img填了个默认图片，是因为即使src为空它依然会向服务器发送请求
<img src="loading.png" data-url="logo1.png"/>
<img src="loading.png" data-url="logo2.png"/>
<img src="loading.png" data-url="logo3.png"/>

//方法一:使用视口API计算实现
var num = document.getElementsByTagName('img').length;
var img = document.getElementsByTagName("img");
var n = 0; //存储图片加载到的位置，避免每次都从第一张图片开始遍历
//页面载入完毕加载可视区域内的图片
lazyload();
//监听页面滚动事件
window.onscroll = lazyload;
function lazyload() { 
	//可见区域高度
	var seeHeight = document.documentElement.clientHeight; 
	//滚动条距离顶部高度
	var scrollTop = document.documentElement.scrollTop || document.body.scrollTop; 
	for (var i = n; i < num; i++) {
		if (img[i].offsetTop < seeHeight + scrollTop) {
			if (img[i].getAttribute("src") == "default.png") {
				img[i].src = img[i].getAttribute("data-src");
			}
		n = i + 1;
		}
	}
}

//方法二:使用IntersectionObserver API实现
function query(selector) {
  return Array.from(document.querySelectorAll(selector));
}

var observer = new IntersectionObserver(
  function(changes) {
    changes.forEach(function(change) {
      var container = change.target;
      var content = container.querySelector('template').content;
      container.appendChild(content);
      observer.unobserve(container);
    });
  }
);

query('.lazy-loaded').forEach(function (item) {
  observer.observe(item);
});
```

## 预加载原理及思路
预加载：就是页面打开，图片什么的都加载好了（优先显示图片）。
核心原理：先将要浏览的图片通过new Image()下载到本地，让浏览器保存起来。

###预加载demo
``` bash
//方法一(CSS+JS),预存至看不见的位置，之后调用快速方便
#pic1{ display:none;background: url(01.png) no-repeat -9999px -9999px; }
#pic2{ display:none;background: url(02.png) no-repeat -9999px -9999px; }
#pic3{ display:none;background: url(03.png) no-repeat -9999px -9999px; }
function preloader() {
 if (document.getElementById) {
  document.getElementById("preload-01").style.background = "url() no-repeat -9999px -9999px";
  document.getElementById("preload-02").style.background = "url() no-repeat -9999px -9999px";
  document.getElementById("preload-03").style.background = "url() no-repeat -9999px -9999px";
 }
}
function addLoadEvent(func) {
 var oldonload = window.onload;
 if (typeof window.onload != 'function') {
  window.onload = func;
 } else {
  window.onload = function() {
   if (oldonload) {
    oldonload();
   }
   func();
  }
 }
}
addLoadEvent(preloader);
//方法二(仅使用JS)
<div style="display: none;">
<script type="text/javascript">
var imageUrls = [url1,url2,url3];
function preloader(imageUrls) {
 var images = [];
 if (document.images) {
  for (i = 0; i < imageUrls.length; i++) {
   images[i] = new Image();
   images[i].src = imageUrls[i];
  }
 }
}
function addLoadEvent(func,imageUrls) {
 var oldonload = window.onload;
 if (typeof window.onload != 'function') {
  window.onload = func;
 } else {
  window.onload = function() {
   if (oldonload) {
    oldonload();
   }
  func(imageUrls);
  }
 }
}
addLoadEvent(preloader, imageUrls);
</script>
</div>

//方法三(AJAX实现)
//load CSS,JS,IMG
window.onload = function() {
 var images = [];
 setTimeout(function() {
  // a new CSS
	var head = document.getElementsByTagName('head')[0];
	var css = document.createElement('link');
	css.type = "text/css";
	css.rel  = "stylesheet";
	css.href = "preload.css";
	// a new JS
  var js  = document.createElement("script");
  js.type = "text/javascript";
  js.src  = "preload.js";
  head.appendChild(css);
  head.appendChild(js);
 }, 1000);
};
//preload.js
window.onload = function(){
// XHR to request a JS and a CSS
 setTimeout(function(){
  var xhr = new XMLHttpRequest();
  xhr.open('GET','preload.php');
  xhr.send('preload');
  var json = JSON.parse(xhr.responseText());
  // preload image
  for(var i in json.imgUrls){
   var image = new Image();
   image.src = json.imgUrls[i].src;
   images.push(image);
  } 
 },1000);
};

//利用img.complete判断加载情况(各大浏览器兼容)
function loadImage(url, callback) { 
 var img = new Image(); //创建一个Image对象，实现图片的预下载 
 img.src = url; 
 if (img.complete) { 
  // 如果图片已经存在于浏览器缓存，直接回调或返回
  return; // 直接返回，不用再处理onload事件 
 } 
 img.onload = function () { 
  //图片下载完毕时异步调用callback函数
 }; 
}; 
```

## 小结
在网上看到有人分享他的面试经历，看到有考懒加载就分析了一下，其注重考了html元素基本属性及JS对于浏览器窗口的取值问题。发现了自己的基础不够夯实还有待提高，时不时得复习一下基础知识是很有必要的。这一问题还有一个点是有待解决的，如对于高频触发的滚轮监听事件如何使其对影响浏览器性能降到最低 :-)