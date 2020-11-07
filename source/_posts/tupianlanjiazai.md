---
title: 实现图片的懒加载
date: 2020-10-29 15:16:04
tags: ['JavaScript']
categories: JavaScript
---

### 为什么使用图片懒加载
<!-- more -->

> 当页面的图片很多时，页面的加载速度缓慢。为了加速页面的加载速度，需要将页面未出现在可视区域的图片不做加载，等到滚动到可视区域再去加载。

### 原理

浏览器加载图片是根据img标签的src属性是否为空，可以定义data-src的自定义属性指向真实图片，src属性为空或指向一张默认图片。当img的图片的offsetTop小于可见区域高度document.documentElement.clientHeight加滚动条距离顶部高度document.documentElement.scrollTop || document.body.scrollTop。复制给img的src的真实图片地址。

```
img {
  display: block;
  margin-bottom: 50px;
  width: 400px;
  height: 1000px;
}

<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww1.sinaimg.cn/large/006y8mN6gw1fa7kaed2hpj30sg0l9q54.jpg" alt="">
<img src="" data-src="http://ww1.sinaimg.cn/large/006y8mN6gw1fa7kaed2hpj30sg0l9q54.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww1.sinaimg.cn/large/006y8mN6gw1fa7kaed2hpj30sg0l9q54.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">
<img src="" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" alt="">

var num = document.getElementsByTagName('img').length
var img = document.getElementsByTagName('img')
// 存储图片加载到的位置，避免每次都从第一张图片开始遍历
var n = 0

function lazyload () {
  // 可见区域高度
  var seeHeight = document.documentElement.clientHeight
  // 滚动条距离顶部高度
  var scrollTop = document.documentElement.scrollTop || document.body.scrollTop
  for (var i = n; i < num; i++) {
    if (img[i].offsetTop < seeHeight + scrollTop) {
      if (img[i].getAttribute('src') === '') {
        img[i].src = img[i].getAttribute('data-src')
      }
      n = n + 1
    }     
  }
}

// 简单的节流函数,当页面滚动时，函数会被高频触发，这非常影响浏览器的性能。
// fun 要执行的函数
// delay 延迟
// time  在time时间内必须执行一次
function throttle(fun, delay, time) {
  var timeout,
      startTime = new Date();
  return function() {
      var context = this,
          args = arguments,
          curTime = new Date();
      clearTimeout(timeout);
      // 如果达到了规定的触发时间间隔，触发 handler
      if (curTime - startTime >= time) {
          fun.apply(context, args);
          startTime = curTime;
          // 没达到触发间隔，重新设定定时器
      } else {
          timeout = setTimeout(fun, delay);
      }
  };
}

lazyload()
window.addEventListener('scroll',throttle(lazyload,500,1000));
```
