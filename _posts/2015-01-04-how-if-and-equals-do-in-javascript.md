---
title: Javascript的if判断以及相等运算(==)的类型转换规则
categories:
  - Javascript
tags:
  - Javascript
---
Javascript的有些特性比较令人捉摸不透，例如以下的代码：

<pre>if ('0') alert(true); else alert(false);    //true
if ('0'==true) alert(true); else alert(false);    //false</pre>

按照程序员的正常思维，经过了隐式的类型转换，这两个语句应该得到相同的结果才对。可事实却出人意料。而这一切，都是由Javascript在这两种场景下的不同转换规则造成的。<!--more-->

# Boolean语境下的类型转换

第一行语句if括号里的表达式预期得出一个Boolean值，于是系统自动调用Boolean方法，将&#8217;0&#8217;转换成Boolean值，该转换规则是将**undefined**、**null**、**+0**、**-0**、**NaN**、**&#8221;（空字符串）**返回false，其它的表达式返回true。&#8217;0&#8217;也返回true了。

# 相等运算(==)下的类型转换

第二行语句里有相等运算符，该运算符的转换规则则稍微有些复杂，这行语句得到的奇怪的结果也是因此而起。这里介绍一下相等运算符的转换规则：

1. Boolean值转成数字，false => 0 , true => 1  
2. 如果一边是字符串，另一边是数字，则尝试把字符串转成数字
3. 如果一边是对象，另一边是字符串，则尝试把对象转成字符串  
4. 如果一边是对象，另一边是数字，则尝试把对象转成数字

根据第1条转换规则，先把等号右边的true转换为1，然后根据第2条转换规则，把&#8217;0&#8217;转换成0，再进行比较，结果返回false。

根据以上的转换规则，能得到很多匪夷所思的现象，这里就不一一列举了，感兴趣的亲们可以自己实验一下。

以上的规则对相等运算符还未阐述完全，比如以下的例子：

<pre>if (new Number(0)==new Number(0)) alert(true); else alert(false);    //false
if (new Number(0)==Number(0)) alert(true); else alert(false); //true</pre>

且看

# 相等运算(==)的比较规则

相等运算符的比较规则是：

1. null和undefined相等  
2. 不能把null和undefined转成其它值
3. 如果有一个是NaN，则==返回false，!=返回true
4. 如果两个都是对象，则比较引用值，相等返回true，否则返回false

在上面最后一个例子中，new Number(0)返回的是一个对象，如果两边都是对象，则比较引用值，它们当然是不想等的，而Number(0)返回的是数字0，这样另一边的对象也会转换成数字0，结果它们是相等的。

#  > , >= , < , <= 的运算规则

  1. null , undefined , 布尔值转换成数字
  2. 如果一方是数字，则会把另一方也转换成数字，再进行比较
  3. 如果一方是NaN，则返回false
  4. 如果两边都是字符串，则逐字比较unicode值
  5. 如果一边是字符串，另一边是对象，或者两边都是对象，则把对象转换成字符串，再进行比较

详细的运算规则可见：<a href="http://bclary.com/2004/11/07/#a-11.8.5" target="_blank">http://bclary.com/2004/11/07/#a-11.8.5</a>
