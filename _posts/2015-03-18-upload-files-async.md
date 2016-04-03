---
title: 异步提交文件
categories:
  - Javascript
tags:
  - XMLHttpRequest2
---
异步提交表单对于现代AJAX程序来说是一件轻而易举的事，如果使用jQuery等库的话更无需考虑浏览器的差异，简单的一句<span class="lang:js decode:true  crayon-inline ">$.post</span> 搞定，但如果要上传文件呢？jQuery的异步请求方法并没有提供异步提交文件的功能，这个时候我们可以使用jQuery的Form插件（<a href="http://malsup.com/jquery/form/" target="_blank">http://malsup.com/jquery/form/</a>）来完成这个功能，或者我们可以搞清楚原理，自己实现一个。<!--more-->

# 使用jQuery的form插件实现异步提交文件

使用form插件实现异步提交表单非常简单，只需要针对form表单执行ajaxForm初始化即可

<pre class="lang:xhtml decode:true">&lt;form id="myForm" action="/upload" method="post" enctype="multipart/form-data"&gt;
  描述：&lt;input name="desc"&gt;
  文件：&lt;input name="file" type="file"&gt;
  &lt;button id="submit" type="submit"&gt;提交&lt;/button&gt;
&lt;/form&gt;
&lt;script&gt;
$('#myForm').ajaxForm(function() {
  alert("提交成功");
});
&lt;/script&gt;</pre>

#  XMLHttpRequest2对异步提交表单的支持

现代的浏览器对异步请求提供了功能更强大的支持，这就是我们要隆重介绍的XMLHttpRequest2。顺便提一句，XMLHttpRequest2并不属于HTML5，它是个新的、功能强大的API。我们可以构造XMLHttpRequest2的FormData对象并放进去我们想要的文本、文件等。

<pre class="lang:js decode:true">$('#myForm').on('submit', function() {
    var formElem = this;
    var formData = new FormData();
    $('#myForm').find('input').each(function() {
      if (this.type=='file' && this.files.length&gt;0) {
        for (var i= 0; i&lt;this.files.length; i++) {
          formData.append(this.name, this.files.item(i));
        }
      } else {
        formData.append(this.name, this.value);
      }
    });

    $.ajax({
      url: formElem.action,
      data: formData,
      processData: false,
      contentType: false,
      type: formElem.method,
      success: function(data){
        alert(data);
      }
    });

    return false;
  });</pre>

#  低级浏览器下的解决方法

XMLHttpRequest2只有IE10+才支持，那么对于低版本的浏览器如何实现呢？我们可以构建一个虚拟表单，并提交到iframe里的方式。

<pre class="lang:js decode:true">$('#myForm').on('submit',function() {
    var jQorigForm = $(this);

    // 创建一个用于存放结果的iframe
    var iframeName = 'form-target-'+new Date().valueOf();
    var jQiframe = $('&lt;iframe id="'+iframeName+'" name="'+iframeName+'" style="position:absolute;left:-9999px;top:-9999px"&gt;&lt;/iframe&gt;')
      .appendTo('body');

    // 创建一个临时表单，令其提交到iframe中
    var jQform = jQorigForm.clone()
      .html('')
      .appendTo('body')
      .attr('target',iframeName)
      .css({position:'absolute',left:'-9999px',top:'-9999px'});

    // 拷贝输入框到临时表单
    jQorigForm.find('input').each(function() {
      var jQinput = $(this);
      var jQnewInput = jQinput.clone();
      if (this.type=='file') { // 由于FileInput不能赋值，所以只能交换两个form的元素的权宜之计
        jQinput.before(jQnewInput);
        jQinput.appendTo(jQform);
      } else {
        jQnewInput.appendTo(jQform);
      }
    });

    // 提交临时表单
    jQform.submit().remove();

    // 设个定时从iframe中读取结果
    var interval = setInterval(function() {
      var content = jQiframe.contents().find('body').html();
      if (content) {
        alert(content);
        jQiframe.remove();
        clearInterval(interval);
      }
    }, 400);

    return false;
  });</pre>


# 参考资料

  * <a href="http://www.html5rocks.com/zh/tutorials/file/xhr2/" target="_blank">http://www.html5rocks.com/zh/tutorials/file/xhr2/</a>
  * <a href="http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/" target="_blank">http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/</a>
  * <a href="http://www.cnblogs.com/fengwei/archive/2012/06/22/2558567.html" target="_blank">http://www.cnblogs.com/fengwei/archive/2012/06/22/2558567.html</a>
