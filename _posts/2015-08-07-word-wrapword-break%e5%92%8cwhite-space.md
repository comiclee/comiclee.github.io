---
title: word-wrap,word-break和white-space
author: comiclee
layout: post
permalink: /2015/08/word-wrapword-break%e5%92%8cwhite-space
duoshuo_thread_id:
  - 1286630914637430802
categories:
  - css
---
word-wrap、word-break和white-space这3个属性影响着页面文章的排版，它们看起来比较接近，容易让人混淆。本文就仔细讨论一下他们的用法，让人们理解它们的作用和使用场景，就能更好地掌握页面的排版。<!--more-->

# word-wrap

word-wrap这个属性源自微软的IE浏览器（所以它的浏览器兼容性是很好的），它只有一个功能——设置当一个英文单词太长以致超出容器时，是否将其截断。它有两个取值：

<table class="dataintable">
  <tr>
    <th>
      值
    </th>
    
    <th>
      描述
    </th>
  </tr>
  
  <tr>
    <td>
      normal
    </td>
    
    <td>
      只在允许的断字点换行（浏览器保持默认处理）。
    </td>
  </tr>
  
  <tr>
    <td>
      break-word
    </td>
    
    <td>
      在长单词或 URL 地址内部进行换行。
    </td>
  </tr>
</table>

默认取值是normal，也就是说长单词不会在中间断行，而是会直接超出去，并且长单词会另起一行放置，如下面这个例子：

<div style="width: 50px; border: 1px dotted gray; word-wrap: normal;">
  你是abcdefghijklmnop吗？
</div>

&nbsp;

大多数情况我们是不想要这样的，我们希望长单词在中间断行，以满足容器尺寸的限制，那么我们就可以加上 <span class="lang:css decode:true  crayon-inline ">word-wrap:break-word</span> ，效果如下：

<div style="width: 50px; border: 1px dotted gray; word-wrap: break-word;">
  你是abcdefghijklmnop吗？
</div>

&nbsp;

虽然单词截断了，但也不太影响阅读，布局也美观了。

# word-break

那word-break是什么作用呢？我们发现刚才用word-wrap仍然没有完美解决问题，看如下的例子：

<div style="width: 100px; border: 1px dotted gray; word-wrap: break-word;">
  你是abcdefghijklmnop吗？
</div>

&nbsp;

虽然长单词截断了，但单词还是另起一行放置，这仍然不够美观，于是word-break就登场了。word-break设置怎样在单词内断行。一般文档都没有太详细的介绍

<table class="dataintable">
  <tr>
    <td>
      normal
    </td>
    
    <td>
      使用浏览器默认的换行规则。
    </td>
  </tr>
  
  <tr>
    <td>
      break-all
    </td>
    
    <td>
      允许在单词内换行。
    </td>
  </tr>
  
  <tr>
    <td>
      keep-all(chrome未实现)
    </td>
    
    <td>
      只能在半角空格或连字符处换行。
    </td>
  </tr>
</table>

由于keep-all值在chrome和opera中未实现，所以使用得比较少，我们常常用的是break-all，使用后的效果如下：

<div style="width: 100px; border: 1px dotted gray; word-wrap: normal; word-break: break-all;">
  你是abcdefghijklmnop吗？
</div>

&nbsp;

这就是我们常常看到的<span class="lang:js decode:true  crayon-inline">word-wrap:break-word; word-break:break-all;</span> 组合用法的效果。

以上的属性的显示效果在桌面浏览器上是ok的，但在手机浏览器safari上，会出现标点符号居于行首的问题。这在文章的排版中是要尽量避免的。经试验，设置<span class="lang:js decode:true  crayon-inline ">word-break:normal</span> 就不会有这个问题，但又回到了刚才的讨论，这需要结合场景来讨论。在没有长单词的情况下，我们大可以设置<span class="lang:js decode:true  crayon-inline ">word-break:normal</span> 以避免出现标点符号出现在行首的问题。

# white-space

最后说说white-space，这个属性用于指示如何处理文本中的空白，空白包括空格、换行符等空白字符。我们最常用的是nowrap属性，即文本不换行，用上这个属性后，word-wrap和word-break都形同虚设，所有都不会换行了。

<table class="dataintable">
  <tr>
    <td>
      normal
    </td>
    
    <td>
      默认。空白会被浏览器忽略。
    </td>
  </tr>
  
  <tr>
    <td>
      pre
    </td>
    
    <td>
      空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。
    </td>
  </tr>
  
  <tr>
    <td>
      nowrap
    </td>
    
    <td>
      文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。
    </td>
  </tr>
  
  <tr>
    <td>
      pre-wrap
    </td>
    
    <td>
      保留空白符序列，但是正常地进行换行。
    </td>
  </tr>
  
  <tr>
    <td>
      pre-line
    </td>
    
    <td>
      合并空白符序列，但是保留换行符。
    </td>
  </tr>
  
  <tr>
    <td>
      inherit
    </td>
    
    <td>
      规定应该从父元素继承 white-space 属性的值。
    </td>
  </tr>
</table>

下表能更清晰地分辨出各属性值的功能

<table class="dataintable">
  <tr>
    <th>
    </th>
    
    <th>
      换行符
    </th>
    
    <th>
      空格和制表符
    </th>
    
    <th>
      文字转行
    </th>
  </tr>
  
  <tr>
    <td>
      <span style="font-family: Consolas, Monaco, 'Lucida Console', monospace;">nomal</span>
    </td>
    
    <td>
      合并
    </td>
    
    <td>
      合并
    </td>
    
    <td>
      转行
    </td>
  </tr>
  
  <tr>
    <td>
      <code>nowrap</code>
    </td>
    
    <td>
      合并
    </td>
    
    <td>
      合并
    </td>
    
    <td>
      不转行
    </td>
  </tr>
  
  <tr>
    <td>
      <code>pre</code>
    </td>
    
    <td>
      保留
    </td>
    
    <td>
      保留
    </td>
    
    <td>
      不转行
    </td>
  </tr>
  
  <tr>
    <td>
      <code>pre-wrap</code>
    </td>
    
    <td>
      保留
    </td>
    
    <td>
      保留
    </td>
    
    <td>
      转行
    </td>
  </tr>
  
  <tr>
    <td>
      <code>pre-line</code>
    </td>
    
    <td>
      保留
    </td>
    
    <td>
      合并
    </td>
    
    <td>
      转行
    </td>
  </tr>
</table>

&nbsp;