---
title: icomoon生成的字体在安卓的一些浏览器上无法显示的问题
categories:
  - 移动前端
---
这个bug发生的条件不普遍，就是在小米2上的微信webview中，原想跳过不管的，不过由于不知道还有哪些机器会受此牵连，这会极大影响我使用icon fonts的信心，故上网搜搜有没有解决方案，终于找到解决方案，还挺简单的。<!--more-->

详见这篇文章<a href="http://www.cnblogs.com/Megasu/p/4305116.html" target="_blank">http://www.cnblogs.com/Megasu/p/4305116.html</a>

总结一下挺简单的，icomoon生成的font排列顺序是：

<pre class="lang:js decode:true ">@font-face {font-family: "iconfont";
  src: url('iconfont.eot'); /* IE9*/
  src: url('iconfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('iconfont.woff') format('woff'), /* chrome、firefox */
  url('iconfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
  url('iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
}</pre>

我们调节一下加载顺序，改成eto,svg,woff,ttf(把SVG放到前面) 即可

<pre class="lang:js decode:true">@font-face {font-family: "iconfont";
  src: url('../font/iconfont.eot'); /* IE9*/
  src: url('../font/iconfont.svg#iconfont') format('svg'), /* iOS 4.1- */
  url('../font/iconfont.woff') format('woff'), /* chrome、firefox */
  url('../font/iconfont.ttf') format('truetype'); /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
}</pre>

&nbsp;
