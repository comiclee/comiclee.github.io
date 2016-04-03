---
title: BFC、浮动、清除和hasLayout
categories:
  - css
tags:
  - css
---
BFC——Block Formatting Context，块级格式化上下文，是CSS渲染的一个概念。我们在大多数的使用场景下并没有受到这个概念的影响，或者我们了解表象却不明白原因，所以容易忽视它，但遇到一些“诡异”的排版问题时，了解这个概念能够让我们更好地认清问题的本质。比如本文着重讲的和浮动有关的问题。<!--more-->

# 产生BFC的条件

以下情况会创建一个BFC：

  * 根元素或其它包含它的元素
  * 浮动 (元素的 float 不为 none)
  * 绝对定位元素 (元素的 position 为 absolute 或 fixed)
  * 内联块 inline-blocks (元素的 display: inline-block)
  * 表格单元格 (元素的 display: table-cell，HTML表格单元格默认属性)
  * 表格标题 (元素的 display: table-caption, HTML表格标题默认属性)
  *  overflow 的值不为 visible的元素
  * 弹性盒 flex boxes (元素的 display: flex 或 inline-flex)

要注意区分BFC和块级元素，它们是不一样的概念，从上面的产生BFC的条件看，并非每个块级元素都会创建BFC。BFC相当于是一个虚拟空间，创造BFC的元素的子元素就处在这个空间中，而如果子元素符合创造BFC的条件，那么它也会创造独立的BFC，它的子孙就处于新的BFC中了。

# BFC对于布局的影响

根据w3c的规范，在一个BFC中，盒子(boxes)从BFC的顶端往下，一个挨着一个垂直放置。相邻盒子之间的垂直距离取决于它们的margin属性，同一个BFC中，相邻块级盒子之间的垂直margin值会折叠。这句话包含几个限定条件，边距折叠的分析在以后的文章中再展开，这次仅注意到**边距折叠只发生在同一个BFC中**。

# BFC对浮动的影响

理解BFC的概念能够更好地理解浮动和清除时出现的现象。  
根据w3c的规范，在BFC中，每个盒子的左外边缘(edge)都会触及BFC的左边缘，甚至浮动元素存在的时候也是这样（尽管盒子的行盒*line boxes*会因为浮动元素而被压缩），除非盒子创建了一个新的BFC。

例1：为了理解上面的话，我们做一个简单的实验，在3个盒子中设置中间的盒子是float:left的，可以看到，浮动元素后面的元素仍然靠着左边缘，尽管文字被挤压了。

<table width="799.0" cellspacing="0" cellpadding="0">
  <tr>
    <td valign="top">
      <style><br /> .small {width:50px; height:50px;}<br /> .middle {width:100px; height:100px;}<br /> .big {width:150px; height:150px;}<br /> .red {background-color:red;opacity:.5;}<br /> .yellow {background-color:yellow;opacity:.5;}<br /> .blue {background-color:blue;opacity:.5;}<br /> .float {float:left}<br /> .clear {clear:both}<br /> .newbfc {overflow:auto}<br /> </style><br /> <div class=&#8221;middle yellow&#8221;></div><br /> <div class=&#8221;middle red float&#8221;>哈哈哈哈哈哈哈哈哈哈</div><br /> <div class=&#8221;big blue&#8221;>哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈</div>
    </td>
  </tr>
</table>

<img class="alignnone  wp-image-155" src="http://blog.comiclee.com/wp-content/uploads/2015/02/1.jpeg" alt="例1" width="152" height="235" />

例2：接下来，给浮动元素后面的div设置overflow:auto，使其创建BFC，于是它跑到浮动元素的右边了。这里选择overflow:auto，因为这个属性自身没有排列布局的功效，但是因为这样设置创建了BFC，最终影响到了它的位置

<table width="799.0" cellspacing="0" cellpadding="0">
  <tr>
    <td valign="top">
      <div class=&#8221;middle yellow&#8221;></div><br /> <div class=&#8221;middle red float&#8221;>哈哈哈哈哈哈哈哈哈哈</div><br /> <div class=&#8221;big blue <span style="color: #ff2600;">newbfc</span>&#8220;>哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈</div>
    </td>
  </tr>
</table>

<img class="alignnone  wp-image-156" src="http://blog.comiclee.com/wp-content/uploads/2015/02/2.jpeg" alt="例2" width="244" height="239" />

# 浮动和清除

clear属性用于清除浮动，它将元素移动到浮动元素的下方。而该属性起作用的元素并不需要是浮动元素的兄弟元素，为了解释这一设定，可以构造出复杂的嵌套来分析其产生特殊布局的原因。不过我们还是把问题说得简单一点，以便于理解。

例3：我们仍然对例1进行一些修改，这一次，我们在蓝色的盒子里放一个小的带有clear属性的盒子，同时再给蓝色的盒子设置一个左边距

<table width="799.0" cellspacing="0" cellpadding="0">
  <tr>
    <td valign="top">
      <div class=&#8221;middle yellow&#8221;></div><br /> <div class=&#8221;middle red float&#8221;>哈哈哈哈哈哈哈哈哈哈</div><br /> <div class=&#8221;big blue&#8221; style=&#8221;margin-left:150px&#8221;><br /> <div class=&#8221;small yellow <span style="color: #ff2600;">clear</span>&#8220;></div><br /> 哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈<br /> </div>
    </td>
  </tr>
</table>

<img class="alignnone  wp-image-157" src="http://blog.comiclee.com/wp-content/uploads/2015/02/3.jpeg" alt="例3" width="250" height="279" />  
结果是不是和你想的一样呢？哈哈。蓝色盒子里的小黄盒子（由于颜色叠加的原因，它变成了不知什么色）左边距离浮动的红盒子那么远，可还是不敢和红盒平起平坐，只能委屈地在红盒的下面。因为蓝盒及其子元素仍然处在先前的BFC中，所以它的clear属性是仍然能和浮动元素发生作用的，这一场景我们常常能在运用浮动时遇到，特别是在蓝盒中也有浮动元素的情况下，如下面例4

例4：

<table width="799.0" cellspacing="0" cellpadding="0">
  <tr>
    <td valign="top">
      <div class=&#8221;middle yellow&#8221;></div><br /> <div class=&#8221;middle red <span style="color: #ff2600;">float</span>&#8220;>哈哈哈哈哈哈哈哈哈哈</div><br /> <div class=&#8221;big blue&#8221; style=&#8221;margin-left:150px&#8221;><br /> <div class=&#8221;small yellow <span style="color: #ff2600;">float</span>”>5</div><br /> <div class=&#8221;small yellow <span style="color: #ff2600;">clear</span>”>4</div><br /> 哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈<br /> </div>
    </td>
  </tr>
</table>

<img class="alignnone  wp-image-158" src="http://blog.comiclee.com/wp-content/uploads/2015/02/4.jpeg" alt="例4" width="248" height="270" />  
我们只是想让div4清除div5的浮动的影响，但是因为红色浮动元素的影响，导致它并没有直接排在div5下边，而是排在红色块下缘的水平线以下。那如何达到我们想实现的效果呢？借用前面说的BFC的知识，我们给蓝色方块创建一个BFC：

例5：

<table width="799.0" cellspacing="0" cellpadding="0">
  <tr>
    <td valign="top">
      <div class=&#8221;middle yellow&#8221;></div><br /> <div class=&#8221;middle red float&#8221;>哈哈哈哈哈哈哈哈哈哈</div><br /> <div class=&#8221;big blue <span style="color: #ff2600;">newbfc</span>&#8221; style=&#8221;margin-left:150px&#8221;><br /> <div class=&#8221;small yellow float&#8221;>5</div><br /> <div class=&#8221;small yellow clear&#8221;>4</div><br /> 哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈<br /> </div>
    </td>
  </tr>
</table>

<img class="alignnone  wp-image-159" src="http://blog.comiclee.com/wp-content/uploads/2015/02/5.jpeg" alt="例5" width="226" height="195" />  
创建BFC有很多方法，都会产生一些副作用，所以按照实际需求选用一个即可。此处也可以选择display:inline-block触发BFC，而保持display:visible的效果，但也会引起其它的副作用，读者可以尝试一下。

# clear元素的边距和clearance

最后介绍一下clearance。清除了浮动的元素，其margin-top值产生的效果可能在你的意料之外，我们继续修改例5，给方块4设置margin-top值为10px，-10px，60px看看.

例6：  
<img class="alignleft  wp-image-160" src="http://blog.comiclee.com/wp-content/uploads/2015/02/61.jpeg" alt="例6" width="178" height="151" /> <img class="alignleft  wp-image-161" src="http://blog.comiclee.com/wp-content/uploads/2015/02/62.jpeg" alt="例6" width="179" height="154" /> <img class="alignleft  wp-image-162" src="http://blog.comiclee.com/wp-content/uploads/2015/02/63.jpeg" alt="例6" width="183" height="154" />

<p style="clear: both;">
  可以看出，margin-top设为10px和-10px都“没有作用”，利用开发者工具看元素的包围情况，可以看到方块4的上边距并没有把它和方块5拉开，好像是举着一根棍子，试图够着蓝色方块的上边缘，可以想象这跟棍子足够长的话就能抵到上边缘，并把方块4顶开（如将margin-top设为60px）。而中间还有的40px的空间就是clearance。
</p>

<img class="alignnone  wp-image-163" src="http://blog.comiclee.com/wp-content/uploads/2015/02/64.jpeg" alt="例6" width="324" height="209" />  
根据w3c的规范，clearance抑制边距折叠，并填充元素margin-top之上的空间，将元素在垂直方向推过浮动元素。它的值恰好让元素的边沿和被清除的浮动元素的下外边沿水平。我们在开发的时候不需要去计算clearance，只需要知道出现这种现象的原因是什么即可。

# IE6,7下的效果

IE8以上的显示效果与w3c的规范一致，但在IE6,7（标准模式）下，显示效果又有所不同。造成这样的效果的原因一方面是由于其自身的bug，另一方面是由Layout（布局）造成的。我们先不讨论Layout，但是需要对例子做一些修改，我们把蓝盒的width和height样式属性去掉，通过增加其内部的文字把蓝盒撑大，并把浏览器窗口缩小以便看得更容易。由于IE6和IE7的显示效果差不多，如果没有太大的差别，我们就只用IE6举例。

例1-1

<pre class="lang:xhtml decode:true">&lt;style&gt;
.small {width:50px; height:50px;}
.middle {width:100px; height:100px;}
.big {width:150px; height:150px;}
.red {background-color:red;filter:alpha(opacity=50);}
.yellow {background-color:yellow;filter:alpha(opacity=50);}
.blue {background-color:blue;filter:alpha(opacity=50);}
.float {float:left}
.clear {clear:both}
.newbfc {overflow:auto}
&lt;/style&gt;
&lt;div class="middle yellow"&gt;&lt;/div&gt;
&lt;div class="middle red float"&gt;哈哈哈哈哈哈哈哈哈哈&lt;/div&gt;
&lt;div class="blue"&gt;
哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈
&lt;/div&gt;</pre>

<img class="alignnone  wp-image-172" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-4@2x.png" alt="例1-1" width="240" height="189" />

其显示效果和例1差不多。然后我们看看例2在IE6下的效果：

例2-1：

<pre class="lang:xhtml decode:true">&lt;div class="middle yellow"&gt;&lt;/div&gt;
&lt;div class="middle red float"&gt;哈哈哈哈哈哈哈哈哈哈&lt;/div&gt;
&lt;div class="blue <span style="color: #ff0000;">newbfc</span>"&gt;
哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈
&lt;/div&gt;</pre>

IE6：

<img class="alignnone  wp-image-172" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-4@2x.png" alt="例1-1" width="240" height="189" />

IE7：

<img class="alignnone  wp-image-173" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-5@2x.png" alt="例2-1" width="232" height="310" />

通过设置overflow:auto，在IE6下无效，在IE7下却能显示出创建了新的BFC后的效果。

例3-1：

<pre class="lang:xhtml decode:true">&lt;div class="middle yellow"&gt;&lt;/div&gt;
&lt;div class="middle red float"&gt;哈哈哈哈哈哈哈哈哈哈&lt;/div&gt;
&lt;div class="blue" style="margin-left:150px"&gt;
  &lt;div class="small yellow clear"&gt;&lt;/div&gt;
  哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈
&lt;/div&gt;</pre>

<img class="alignnone  wp-image-174" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-6@2x.png" alt="例3-1" width="234" height="303" />

咦，好像出现了类似bug的东西。拥有clear属性的方块不但自己跑了下来，还把外层的蓝色方块也拉了下来。嗯，其实这里是发生了边距折叠，我们可以给蓝块设置一个border-top来试验一下

例3-1-1：

<pre class="lang:xhtml decode:true">&lt;div class="middle yellow"&gt;&lt;/div&gt;
&lt;div class="middle red float"&gt;哈哈哈哈哈哈哈哈哈哈&lt;/div&gt;
&lt;div class="blue" style="margin-left:150px;border-top:1px dashed;"&gt;
  &lt;div class="small yellow clear"&gt;&lt;/div&gt;
  哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈
&lt;/div&gt;</pre>

<img class="alignnone  wp-image-175" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-7@2x.png" alt="例3-1-1" width="224" height="229" />

但这里的边距折叠是IE6、7的一个bug，因为根据w3c规定，clear元素不会发生边距折叠。

接下来看看例4，这个表现和预想的一致

例4-1：

<img class="alignnone  wp-image-176" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-8@2x.png" alt="例4-1" width="223" height="224" />

例5在IE6和IE7下也是有差别的，IE7同样能显示出创建新bfc后的效果，而IE6却没有变化

例5-1

IE6：

<img class="alignnone  wp-image-177" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-9@2x.png" alt="例5-1" width="216" height="196" />

IE7：

<img class="alignnone  wp-image-178" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-10@2x.png" alt="例5-1" width="212" height="179" />

最后我们看看clearance在IE6、7下的表现如何

例6-1

IE6

<img class="alignnone  wp-image-179" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-13@2x.png" alt="例6-1" width="212" height="197" /><img class="alignnone  wp-image-180" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-14@2x.png" alt="例6-1" width="198" height="196" />

IE7

<img class="alignnone  wp-image-181" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-11@2x.png" alt="例6-1" width="204" height="179" /><img class="alignnone  wp-image-182" src="http://blog.comiclee.com/wp-content/uploads/2015/02/QQ20150213-12@2x.png" alt="例6-1" width="210" height="178" />

在IE6、7下根本没有clearance的概念，它的行为却更符合我们一般的认识。由于IE6、7和其它浏览器的这一差别，<span style="color: #ff0000;"><strong>我们对待clear元素要更加慎重一些，尽量避免需要专门给IE6、7做hack的情况</strong></span>。

最后我们看看为什么newbfc这个class在IE7下似乎是生效的，而在IE6下却不生效。这就引出IE6、7下特有的概念——hasLayout

# hasLayout

hasLayout是在IE6、7下特有的概念，在IE8以后就废弃了。一个元素触发hasLayout的效果类似于其创建了新的BFC。

那么要如何触发hasLayout呢？默认拥有布局的元素有：

html，body，table，tr，th，td，iframe，object, applet，img，hr，input，button，select，textarea，fieldset，legend等。

设置以下属性会使一个元素拥有布局：

  * position:absolute
  * float:left or right
  * display:inline-block
  * width:any value other than auto
  * height:any value other than auto
  * zoom:any value other than normal
  * writing-mode:tb-rl

IE7还可以通过以下属性设置hasLayout：

  * overflow:hidden or scroll or auto
  * overflow-x:hidden or scroll or auto
  * overflow-y:hidden or scroll or auto
  * min-width:any value other than auto
  * max-width:any value other than auto
  * min-height:any value other than auto
  * max-height:any value other than auto

可以看出，在上面的例子中，设置overflow:auto是可以在IE7中触发hasLayout的，而在IE6中不行，不过只需要给元素设置width或height属性就可以让其拥有布局。所以我们在布局的时候，为了达到浏览器显示的一致性，往往需要考虑<span style="color: #ff0000;"><strong>同时触发hasLayout以及创建BFC</strong></span>。

# 参考资料

  1. <a href="http://www.w3.org/TR/CSS21/visuren.html#block-formatting" target="_blank">http://www.w3.org/TR/CSS21/visuren.html#block-formatting</a>
  2. <a href="https://developer.mozilla.org/zh-CN/docs/Web/CSS/Block_formatting_context" target="_blank">https://developer.mozilla.org/zh-CN/docs/Web/CSS/Block_formatting_context</a>
  3. <a href="http://www.sitepoint.com/web-foundations/internet-explorer-haslayout-property/" target="_blank">http://www.sitepoint.com/web-foundations/internet-explorer-haslayout-property/</a>
