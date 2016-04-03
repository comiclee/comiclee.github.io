---
title: windows phone IE 的弹性盒写法
categories:
  - 移动前端
tags:
  - css3
---
windows phone下的弹性盒使用了某个中间草案的规范，和webkit不一样，可见 <a href="http://msdn.microsoft.com/en-us/library/ie/hh673531(v=vs.85).aspx" target="_blank">http://msdn.microsoft.com/en-us/library/ie/hh673531(v=vs.85).aspx</a> 。好在大致的概念差不多，只是写法有点差异，注意属性的键名都不同：

<pre class="lang:css decode:true ">.flexbox,
.flexboxv {
  display: -webkit-box;
  display: -ms-flexbox;
  display: -moz-box;
  display: box;
}

.flexboxv {
  -webkit-box-orient: vertical;
  -ms-flex-direction: column;
  -moz-box-orient: vertical;
  box-orient: vertical;
}

.flex {
  -webkit-box-flex:1;
  -ms-flex: 1 1 auto;
  -moz-box-flex:1;
  box-flex:1;
}</pre>

&nbsp;
