---
title: Date的迷思
categories:
  - Javascript
---
不得不说Javascript的Date类的功能实在比较弱，没有format功能，导致创建和打印的时候都要多费一些代码。对于创建Date对象，有四种方法可以用：<!--more-->

<pre class="lang:js decode:true ">new Date();
new Date(value);
new Date(dateString);
new Date(year, month, day, hour, minute, second, millisecond);</pre>

其中1、2、4的参数比较确定，不用多说。而方法3的dateString是什么格式？这是需要注意的地方。该参数的解释是这样的：

> 表示日期的字符串值。该字符串应该能被 Date.parse() 方法识别（符合 <a href="http://tools.ietf.org/html/rfc2822#page-14" target="_blank">IETF-compliant RFC 2822 timestamps</a> 或 <a href="http://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15" target="_blank">version of ISO8601</a>）。

写这种字符串是什么体验？

<pre class="lang:js decode:true">new Date("Apr 14, 2015");
new Date("Tue, 14 Apr 2015 21:06:25 GMT+0800");</pre>

这种不太符合中国人习惯的风格写起来真是别扭啊。需要摸索一个简洁的写法，比如说&#8221;2015-04-14&#8243;，这种格式行不行呢？在几款浏览器下做了实验，并不是完全兼容的。

<table>
  <tr>
    <th>
    </th>

    <th>
      Chrome
    </th>

    <th>
      IE9,10
    </th>

    <th>
      IE6-8
    </th>
  </tr>

  <tr>
    <td>
       new Date(&#8216;2015-04-14&#8242;)
    </td>

    <td>
       <span style="color: #ff0000;">Tue Apr 14 2015 08:00:00 GMT+0800 (CST)</span>
    </td>

    <td>
      <span style="color: #ff0000;"> Tue Apr 14 08:00:00 UTC+0800 2015</span>
    </td>

    <td>
      <span style="color: #ff0000;"> NaN</span>
    </td>
  </tr>

  <tr>
    <td>
      new Date(&#8216;2015-4-14&#8242;)
    </td>

    <td>
       Tue Apr 14 2015 00:00:00 GMT+0800 (CST)
    </td>

    <td>
        <span style="color: #ff0000;">Invalid Date</span>
    </td>

    <td>
       <span style="color: #ff0000;">NaN</span>
    </td>
  </tr>

  <tr>
    <td>
      new Date(&#8216;2015-04-14 21:12:25&#8242;)
    </td>

    <td>
       Tue Apr 14 2015 21:12:25 GMT+0800 (CST)
    </td>

    <td>
      <span style="color: #ff0000;"> Invalid Date</span>
    </td>

    <td>
      <span style="color: #ff0000;"> NaN</span>
    </td>
  </tr>

  <tr>
    <td>
       new Date(&#8216;2015-4-14 21:12:25&#8242;)
    </td>

    <td>
       Tue Apr 14 2015 21:12:25 GMT+0800 (CST)
    </td>

    <td>
       <span style="color: #ff0000;">Invalid Date</span>
    </td>

    <td>
       <span style="color: #ff0000;">NaN</span>
    </td>
  </tr>

  <tr>
    <td>
       new Date(&#8216;2015/04/14&#8242;)
    </td>

    <td>
      Tue Apr 14 2015 00:00:00 GMT+0800 (CST)
    </td>

    <td>
      Tue Apr 14 00:00:00 UTC+0800 2015
    </td>

    <td>
       Tue Apr 14 00:00:00 UTC+0800 2015
    </td>
  </tr>

  <tr>
    <td>
      new Date(&#8216;2015/4/14&#8242;)
    </td>

    <td>
      Tue Apr 14 2015 00:00:00 GMT+0800 (CST)
    </td>

    <td>
       Tue Apr 14 00:00:00 UTC+0800 2015
    </td>

    <td>
      Tue Apr 14 00:00:00 UTC+0800 2015
    </td>
  </tr>

  <tr>
    <td>
       new Date(&#8217;15/04/14&#8242;)
    </td>

    <td>
      <span style="color: #ff0000;"> Invalid Date</span>
    </td>

    <td>
       <span style="color: #ff0000;">Thu Mar 4 00:00:00 UTC+0800 1915</span>
    </td>

    <td>
       <span style="color: #ff0000;">Thu Mar 4 00:00:00 UTC+0800 1915</span>
    </td>
  </tr>

  <tr>
    <td>
       new Date(&#8216;2015/04/14 21:06:25&#8242;)
    </td>

    <td>
       Tue Apr 14 2015 21:06:25 GMT+0800 (CST)
    </td>

    <td>
       Tue Apr 14 21:06:25 UTC+0800 2015
    </td>

    <td>
      Tue Apr 14 21:06:25 UTC+0800 2015
    </td>
  </tr>

  <tr>
    <td>
      new Date(&#8216;2015/4/14 21:6:25&#8242;)
    </td>

    <td>
      Tue Apr 14 21:06:25 UTC+0800 2015
    </td>

    <td>
       Tue Apr 14 21:06:25 UTC+0800 2015
    </td>

    <td>
      Tue Apr 14 21:06:25 UTC+0800 2015
    </td>
  </tr>
</table>

从以上浏览器的比较中可以看出形如&#8221;yyyy-MM-dd&#8221;在IE中的兼容性不佳，亦有时区的问题。而**&#8220;yyyy/MM/dd&#8221;**以及**&#8220;yyyy/MM/dd HH:mm:ss&#8221;**在各浏览器下的兼容性比较好，推荐使用这个。
