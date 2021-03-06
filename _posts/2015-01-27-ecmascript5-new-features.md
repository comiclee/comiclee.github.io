---
title: ECMAScript5新特性
categories:
  - Javascript
tags:
  - Javascript
---
现在写这篇文章有点晚了，毕竟ECMAScript5在2009年就发布了。但是当时支持的浏览器还很少，经过这几年的发展，主流浏览器对ECMAScript5已经大都支持了（不过IE8仍然占有很大的份额），我们再重新看看ECMAScript5里有哪些新特性我们可用的。

ECMAScript支持的浏览器为IE9+，chrome23+，safari6+，IOS7/8，其它份额很小的浏览器我就没有列出来了，具体可以看 <http://kangax.github.io/compat-table/es5/>

<!--more-->

<table cellspacing="0" cellpadding="0" style="table-layout: fixed">
  <tr>
    <td valign="top">
      Object.create
    </td>

    <td valign="top">
      创建一个拥有指定原型和若干个指定属性的对象
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.defineProperty
    </td>

    <td valign="top">
      该方法允许精确添加或修改对象的属性。正常的属性添加通过赋值来创建并显示在属性枚举中（for&#8230;in 循环 或 Object.keys 方法）， 这种方式添加的属性值可能被改变，也可能会被 删除。该方法允许改变这些额外细节的默认设置。
    </td>

    <td valign="top">
      可以定义这个属性的特性，如writable, get, set, confiurable, enumerable
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.defineProperties
    </td>

    <td valign="top">
      批量defineProperty
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.getPrototypeOf
    </td>

    <td valign="top">
      返回指定对象的原型（也就是该对象内部属性[[Prototype]]的值）
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.keys
    </td>

    <td valign="top">
      返回一个由给定对象的所有可枚举自身属性的属性名组成的数组，数组中属性名的排列顺序和使用for-in循环遍历该对象时返回的顺序一致（两者的主要区别是 for-in 还会遍历出一个对象从其原型链上继承到的可枚举属性）
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.seal
    </td>

    <td valign="top">
      让一个对象密封，并返回被密封后的对象。密封对象是指那些不能添加新的属性，不能删除已有属性，以及不能修改已有属性的可枚举性、可配置性、可写性，但可能可以修改已有属性的值的对象
    </td>

    <td valign="top">
      检测：Object.isSealed
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.freeze
    </td>

    <td valign="top">
      冻结一个对象。冻结对象是指那些不能添加新的属性，不能修改已有属性的值，不能删除已有属性，以及不能修改已有属性的可枚举性、可配置性、可写性的对象。也就是说，这个对象永远是不可变的。该方法返回被冻结的对象
    </td>

    <td valign="top">
      和seal相比，值都不能改了检测：Object.isFrozen
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.preventExtensions
    </td>

    <td valign="top">
      让一个对象变的不可扩展，也就是永远不能再添加新的属性
    </td>

    <td valign="top">
      检测：Object.isExtensible
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.getOwnPropertyDescriptor
    </td>

    <td valign="top">
      返回指定对象上一个自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）
    </td>

    <td valign="top">
      返回其writable, get, set, confiurable, enumerable属性
    </td>
  </tr>

  <tr>
    <td valign="top">
      Object.getOwnPropertyNames
    </td>

    <td valign="top">
      返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性）组成的数组。
    </td>

    <td valign="top">
      和keys的区别是它可以列出不可枚举的值
    </td>
  </tr>

  <tr>
    <td valign="top">
      Date.prototype.toISOString
    </td>

    <td valign="top">
      返回一个 ISO（ISO 8601 Extended Format）格式的字符串： YYYY-MM-DDTHH:mm:ss.sssZ。时区总是UTC（协调世界时），加一个后缀“Z”标识
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Date.now
    </td>

    <td valign="top">
      返回自1970年1月1日 00:00:00 UTC到当前时间的毫秒数
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.isArray
    </td>

    <td valign="top">
      判断一个对象是否为数组,如果是,则返回true,否则返回false.
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      JSON
    </td>

    <td valign="top">
      parse 解析JSON字符串, 可以选择改变前面解析后的值及其属性，然后返回解析的值。<br /> stringify 返回指定值的 JSON 字符串，可以自定义只包含某些特定的属性或替换属性值。
    </td>

    <td valign="top">
      <span style="color: #ff2600;">IE8支持</span>
    </td>
  </tr>

  <tr>
    <td valign="top">
      Function.prototype.bind
    </td>

    <td valign="top">
      创建一个新函数,称为绑定函数.当调用这个绑定函数时,绑定函数会以创建它时传入 bind()方法的第一个参数作为 this,传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数.
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      String.prototype.trim
    </td>

    <td valign="top">
      删除字符串两端的空白字符并返回.
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.indexOf
    </td>

    <td valign="top">
      返回根据给定元素找到的第一个索引值，否则返回-1
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.lastIndexOf
    </td>

    <td valign="top">
      返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.every
    </td>

    <td valign="top">
      为数组中的每个元素执行一次 callback 函数，直到它找到一个使 callback 返回 falsy（表示可转换为布尔值 false 的值）的元素。如果发现了一个这样的元素，every 方法将会立即返回 false。否则，callback 为每一个元素返回 true，every 就会返回 true。
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.some
    </td>

    <td valign="top">
      测试数组中的某些元素是否通过了指定函数的测试
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.forEach
    </td>

    <td valign="top">
      让数组的每一项都执行一次给定的函数
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.map
    </td>

    <td valign="top">
      给原数组中的每个元素都按顺序调用一次 callback 函数。callback 每次执行后的返回值组合起来形成一个新数组。 callback 函数只会在有值的索引上被调用；那些从来没被赋过值或者使用 delete 删除的索引则不会被调用。
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.filter
    </td>

    <td valign="top">
      filter 为数组中的每个元素调用一次 callback 函数，并利用所有使得 callback 返回“真值”（即可转换为布尔值 true 的值）的元素创建一个新数组。callback 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 callback 测试的元素只会被跳过，不会被包含在新数组中。
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.reduce
    </td>

    <td valign="top">
      接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始缩减，最终为一个值。
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Array.prototype.reduceRight
    </td>

    <td valign="top">
      接受一个函数作为累加器（accumulator），让每个值（从右到左，亦即从尾到头）缩减为一个值。（与 reduce() 的执行方向相反）
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      getter和setter
    </td>

    <td valign="top">
      var o={_name :&#8217;o&#8217;, get name(){return this._name}, set name(v) {this._name=v}};console.log(o.name);    //‘o&#8217;
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Property access on strings
    </td>

    <td valign="top">
      &#8216;foobar'[3]==&#8217;b&#8217;
    </td>

    <td valign="top">
      <span style="color: #ff2600;">IE8支持</span>
    </td>
  </tr>

  <tr>
    <td valign="top">
      保留字作为对象属性
    </td>

    <td valign="top">
      a={if:1}
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      0宽度字符作为名称
    </td>

    <td valign="top">
      var _\u200c\u200d = true;<br /> return _\u200c\u200d;
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      parseInt忽视开头的&#8217;0&#8242;
    </td>

    <td valign="top">
      parseInt(&#8216;010&#8242;) === 10;
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      undefined不可变
    </td>

    <td valign="top">
    </td>

    <td valign="top">
    </td>
  </tr>

  <tr>
    <td valign="top">
      Strict mode
    </td>

    <td valign="top">
      (function () {&#8220;use strict&#8221;;return !this;}())
    </td>

    <td valign="top">
       <span style="color: #ff2600;">IE9不支持</span>
    </td>
  </tr>
</table>
