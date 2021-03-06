---
title: 手机html5页面进行拍照和照片处理
categories:
  - 移动前端
tags:
  - html5
---
<h1 id="id-手机html5页面进行拍照和照片处理-背景" style="color: #333333;">
  <strong>背景</strong>
</h1>

<p style="color: #333333;">
   利用html5的API，在手机上也能够调起摄像头进行拍照、并对照片进行编辑。APP能做的h5也能做到，而且也不复杂。本文就介绍一下如何实现。
</p>

<!--more-->

<h1 id="id-手机html5页面进行拍照和照片处理-调起系统相机" style="color: #333333;">
  <strong>调起系统相机</strong>
</h1>

<p style="color: #333333;">
   使用type值为file，并设置accept属性的input标签，用户点击该元素即可唤起系统的摄像头/图库的菜单。如下：
</p>

<pre style="color: #333333;">&lt;input id="camera" type="file" accept="image/*"&gt;</pre>

<div class="table-wrap" style="color: #333333;">
</div>

<p style="color: #333333;">
  需要注意的是，在安卓的WebView中，改功能默认是关闭的，需要在程序中打开该功能。<br /> 拍完照片后，可以将照片当作文件进行表单提交，但手机拍的照片文件大小一般比较大，要占用不少的宝贵的移动流量，同时也需要用户长时间的等待，所以我们对照片进行一定的压缩再提交。首先，我们需要读取拍到的照片。
</p>

<h1 id="id-手机html5页面进行拍照和照片处理-读取拍到的图片" style="color: #333333;">
  <strong>读取拍到的图片</strong>
</h1>

<p style="color: #333333;">
   html5提供了FileReader对象来读取本地文件，使用readAsDataURL读取文件的base64编码数据
</p>

<pre style="color: #333333;">$('#camera').change(function(evt) {
 if (evt.target.files.length&gt;0) {
  var file = evt.target.files[0];
  var reader = new FileReader();
  reader.onload = function(e) {
   var image = new Image();
   image.onload = function() {
    processImage(image);
   };
   image.src = e.target.result;
  };
  reader.readAsDataURL(file);
 }
});</pre>

<h1 id="id-手机html5页面进行拍照和照片处理-处理照片" style="color: #333333;">
  <strong>处理照片</strong>
</h1>

<p style="color: #333333;">
  使用canvas进行图片处理，我们将图片按比例缩小绘制在canvas上。例如我们将图片的尺寸缩小到0.1
</p>

<pre class="table-wrap" style="color: #333333;">var canvas = document.getElementById('canvas');
var ratio = 0.1;
function processImage(image) {
 var ctx = canvas.getContext(‘2d’);
 canvas.width = image.width*ratio;
 canvas.height = image.height*ratio;
 ctx.clearRect(0,0,canvas.width,canvas.height);
 ctx.drawImage(image, 0, 0, image.width, image.height, 0, 0, canvas.width, canvas.height);
}</pre>

<h1 id="id-手机html5页面进行拍照和照片处理-导出处理后的照片" style="color: #333333;">
  <strong>导出处理后的照片</strong>
</h1>

<p style="color: #333333;">
  使用canvas的toDataURL方法导出处理后的图片的base64编码数据。可以在方法的参数中设定图片格式，默认值为image/png。
</p>

<pre class="table-wrap" style="color: #333333;">canvas.toDataURL('image/jpeg')</pre>

<p style="color: #333333;">
  由于base64格式的数据带有 <a class="external-link" style="color: #3b73af;" href="http://dataimage/" target="_blank" rel="nofollow">data:image/jpeg;base64</a>, 这类的头，后端解析的时候需要把这个头去掉，这可以在前端做，也可以在后端做。
</p>

<h1 id="id-手机html5页面进行拍照和照片处理-不得不提的坑" style="color: #333333;">
  <strong>不得不提的坑</strong>
</h1>

<p style="color: #333333;">
  整个图片的处理流程并不是很复杂，但当我们实际操作的时候，却发现事实远没有那么简单。我们遇到两个大坑：<br /> 1. iphone拍的照片绘制出来会被压扁<br /> 2. 照片的旋转方向不正确
</p>

好在这两个问题有不少开发者也遇到过，我们在网上找到答案：  
1. 照片被压扁的问题可以参考<span style="color: #042eee;"><span style="text-decoration: underline;"><a class="external-link" style="color: #3b73af;" href="http://stackoverflow.com/questions/11929099/html5-canvas-drawimage-ratio-bug-ios" target="_blank" rel="nofollow">http://stackoverflow.com/questions/11929099/html5-canvas-drawimage-ratio-bug-ios</a></span></span> ，利用答案中的detectVerticalSquash函数来对缩放比例进行修正。（这个函数出自<span style="color: #042eee;"><span style="text-decoration: underline;"><a class="external-link" style="color: #3b73af;" href="https://github.com/stomita/ios-imagefile-megapixel" target="_blank" rel="nofollow">https://github.com/stomita/ios-imagefile-megapixel</a></span></span>，感谢stomita的工作）

<pre class="table-wrap" style="color: #333333;">ctx.drawImage(img, sx * vertSquashRatio, sy * vertSquashRatio,
 sw * vertSquashRatio, sh * vertSquashRatio,
 dx, dy, dw, dh );</pre>

<p style="color: #333333;">
  2. 利用Exif.js（<span style="color: #042eee;"><span style="text-decoration: underline;"><a class="external-link" style="color: #3b73af;" href="https://github.com/jseidelin/exif-js" target="_blank" rel="nofollow">https://github.com/jseidelin/exif-js</a></span></span>）读取照片的旋转方向，并对Canvas进行旋转
</p>

<pre class="table-wrap" style="color: #333333;">$('#camera').change(function(evt) {
 if (evt.target.files.length&gt;0) {
  var file = evt.target.files[0];

  var rotation = 0;
  EXIF.getData(file, function() {
   var orientation = EXIF.getTag(this,'Orientation');
   switch (orientation) { //根据exif中照片的旋转信息对图片进行旋转
    case 3: rotation=180; break;
    case 6: rotation=90; break;
    case 8: rotation=-90; break;
    default : rotation=0;
   }

   var reader = new FileReader();
   reader.onload = function(e) {
    var image = new Image();
    image.onload = function() {
     processImage(image, rotation);
    };
    image.src = e.target.result;
   };
   reader.readAsDataURL(file);
  });
 }
});

//修改processImage函数
var canvas = document.getElementById('canvas');
var ratio = 0.1;
function processImage(image,rotation) {
 var ctx = canvas.getContext('2d');
 var imageWidthOrigin = image.width;
 var imageHeightOrigin = image.height;
 var imageWidth = imageWidthOrigin*ratio;
 var imageHeight = imageHeightOrigin*ratio;

 canvas.width = imageWidth ;

 canvas.height = imageHeight ;
 var startX = 0;
 var startY = 0;

 if (rotation==90) {
  canvas.width = imageHeight ;
  canvas.height = imageWidth ;
  startX = 0;
  startY = -imageHeight;
 } else if (rotation==-90) {
  canvas.width = imageHeight ;
  canvas.height = imageWidth ;
  startX = -imageWidth;
  startY = 0;
 } else if (rotation==180 || rotation==-180) {
  startX = -imageWidth;
  startY = -imageHeight;
 }

 ctx.clearRect(0,0,canvas.width,canvas.height);
 ctx.rotate(rotation*Math.PI/180);

 var vertSquashRatio = detectVerticalSquash(image);
 ctx.drawImage(image, 0, 0, imageWidthOrigin*vertSquashRatio, imageHeightOrigin*vertSquashRatio, startX, startY, imageWidth, imageHeight);
}</pre>

<p style="color: #333333;">
