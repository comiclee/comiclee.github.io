---
title: Javascript的严格模式
author: comiclee
layout: post
permalink: /2015/03/javascript%e7%9a%84%e4%b8%a5%e6%a0%bc%e6%a8%a1%e5%bc%8f
duoshuo_thread_id:
  - 1286630914637430799
categories:
  - Javascript
tags:
  - Javascript
---
ECMAScript5定义了一种严格模式的语法，它禁用了一些不够安全的用法，同时，对一些原本静默失败的异常情况会抛出错误。使用严格模式可以帮助我们避免因为失误写出不安全的代码，我们应该尽量使用它。<!--more-->

# 启用严格模式

使用<span class="lang:js decode:true  crayon-inline">&#8220;use strict&#8221;;</span>  或者 <span class="lang:js decode:true  crayon-inline ">&#8216;use strict';</span>  可以指定特定的代码使用严格模式，如我们可以指定对单个函数启用严格模式。如果我们在全局使用严格模式，那么会影响到所有的代码，这可能导致一些旧代码执行出错，所以我们应该把严格模式的代码控制在我们所掌握的范围内。

要注意的是，<span class="lang:js decode:true  crayon-inline">&#8220;use strict&#8221;;</span> 应该放在代码的顶端才能生效，放在中间是不生效的。请看下面的例子：

<pre class="lang:js decode:true">// OK, non-strict mode
(function foo() {
  arguments = 10;
  "use strict";
})();
 
// SyntaxError, strict-mode
(function foo() {
  "use strict";
  arguments = 10;
})();</pre>

当然，这个顶端指的是可执行代码之前，倒不必非是第一行代码：

<pre class="lang:js decode:true ">// also strict mode
 
"use restrict";
"use strict";
 
a = 10; // ReferenceError</pre>

#  严格模式的作用域

严格模式的作用域延伸到其所有内部代码，定义了严格模式的代码，其内部嵌套的代码都处于严格模式中：

<pre class="lang:js decode:true ">// define strict mode in the global context,
// i.e. for the whole program
"use strict";
 
(function foo() {
  // strictness is "inherited" from
  // the global context
  eval = 10; // SyntaxError
  (function bar() {
    // the same - from the global context
    arguments = 10; // SyntaxError
  })();
})();</pre>

需要明确一点是，严格模式作用于词法上下文（静态上下文）而非运行时环境，即在定义的时候其作用域就已经确定了，而非运行时才确定，这一点和闭包一样：

<pre class="lang:js decode:true ">// globally use a non-strict mode
 
var foo = (function () {
  "use strict";
  return function () {
    alert(this);
  };
})();
 
function bar() {
  alert(this);
}
 
// for both functions, a caller is the global context
 
// but "foo" is evaluated in the strict mode
foo(); // undefined
 
// meanwhile "bar" is not
bar(); // object</pre>

另外提及一点，使用Function构造函数创造的函数不会继承定义时的环境的严格模式：

<pre class="lang:js decode:true ">"use strict";
 
var f = Function("eval", "arguments", " \
  eval = 10; arguments = 20; \
  with ({a: 30}) { \
    alert(a + eval + arguments); \
  }"
);
 
f(); // OK, 60</pre>

除非把 <span class="lang:js decode:true  crayon-inline">&#8220;use strict&#8221;;</span> 放在函数定义中：

<pre class="lang:js decode:true ">// non-strict globally
 
var f = Function("eval", "'use strict'; alert(eval);"); // SyntaxError</pre>

#  严格模式下的代码要求和限制

我们看看在严格模式下有哪些代码的要求和限制

## 为未来预留的关键字

这些关键字是为未来预留的，所以不能用于变量名和函数名中：*implements*, *interface*, *let*, *package*, *private*, *protected*, *public*, *static*, *yield* 。

<pre class="lang:js decode:true ">"use strict";
 
var let = 10; // SyntaxError</pre>

##  不支持八进制字面量

在非严格模式中，数字第一位是0会被解析成八进制数字：

<pre class="lang:js decode:true ">var x = 010; // octal number
print(x); // 8 - in octal radix</pre>

这样的做法仅仅是为了向后兼容，但在严格模式下，这样的写法不被支持了，这么写将会报错：

<pre class="lang:js decode:true ">"use strict";
 
var x = 010; // SyntaxError</pre>

另外提一点，在ES3中使用 <span class="lang:js decode:true  crayon-inline ">parseInt(&#8216;010&#8242;)</span> 会默认转成八进制数字，不过这在ES5中已经改为转换成十进制数字了，不管有没有严格模式。

##  给未声明的变量赋值

众所周知，给未声明的变量赋值会自动创建一个全局作用域的变量，这是一种非常不安全的行为：

<pre class="lang:js decode:true ">// non-strict mode
 
(function foo() {
  // local vars
  var x = y = 20;
})();
 
// unfortunately, "y" wasn't local
// for "foo" function
alert(y); // 20
alert(x); // "x" is not defined</pre>

但在严格模式下，这么做将会报错：

<pre class="lang:js decode:true ">"use strict";
a = 10; // ReferenceError
 
var x = y = 20; // also a ReferenceError</pre>

## eval 和 arguments

在严格模式下，eval和arguments被当作关键词。它们不能用作变量名、函数名、函数的参数名，不能给它们赋值，不能进行自增、自减操作：

<pre class="lang:js decode:true">"use strict";
 
// SyntaxError in both cases
var arguments;
var eval;
 
// also SyntaxError
function eval() {}
var foo = function arguments() {};

// SyntaxError
function foo(eval, arguments) {}

(function (x) {
  alert(arguments[0]); // 30
  arguments = 40; // TypeError
})(30);

// SyntaxError
++eval;
arguments--;

try {
  throw Error("...");
} catch (arguments) {} // SyntaxError, the same for "eval" name</pre>

它们可以作为对象的属性名，不过不能作为函数的属性名

<pre class="lang:js decode:true">"use strict";
 
// OK
var foo = {
  eval: 10,
  arguments: 20
};
 
// OK
foo.eval = 10;
foo.arguments = 20;</pre>

<pre class="lang:js decode:true">"use strict";
 
function foo() {
  alert(foo.arguments); // SyntaxError
  alert(foo.caller); // SyntaxError
}
 
foo();</pre>

arguments和函数参数之间的绑定将会断开，即更改一个不会影响到另一个

<pre class="lang:js decode:true ">"use strict";
 
(function foo(x) {
 
  alert(arguments[0]); // 10
  arguments[0] = 20;
 
  alert(x); // 10, but not 20
 
  x = 30;
  alert(arguments[0]); // 20, but not 30
 
})(10);</pre>

eval执行在一个沙盒环境中，在其中声明的变量不会影响到全局的作用域，在eval结束后，沙盒环境也相应地消失。

<pre class="lang:js decode:true ">"use strict";
 
eval("var x = 10; alert(x);"); // 10
alert(x); // "x" is not defined</pre>

不过我们可以通过 indirect eval 来让eval创建全局变量。

<pre class="lang:js decode:true ">"use strict";
 
("indirect", eval)("var x = 10; alert(x);"); // 10
alert(x); // 10</pre>

## callee 和 caller

在严格模式下，callee和caller都被禁止访问。我们无法使用arguments.callee来获取匿名函数了

<pre class="lang:js decode:true ">"use strict";
 
(function foo(bar) {
  if (!bar) {
    arguments.callee(true); // SyntaxError
    foo(true); // OK
  }
})();</pre>

这是处于安全性的考虑，如果没有这样的限制，被调用函数有权限修改调用方的值，比如：

<pre class="lang:js decode:true ">// non-strict mode
 
function foo(x, y) {
  alert(x); // 10
  bar();
  alert(x); // 100
}
 
function bar() {
  console.dir(bar.caller.arguments); // 10, 20
  bar.caller.arguments[0] = 100;
}
 
foo(10, 20);</pre>

禁止访问arguments.callee后会引发一些不便，比如在非全局作用域下，用Function创建函数，且这个函数存在递归调用的时候：

<pre class="lang:js decode:true ">(function () {
   
  // outer name is not available,
  // regardless strictness
  var foo = Function("alert(foo);'");
  foo(); // "foo" is not defined (no such name in the global context)
   
  // error in strict mode for arguments.callee
  Function("'use strict; alert(arguments.callee);'")(); // TypeError
   
  // OK in non-strict for arguments.callee
  Function("alert(arguments.callee);'")(); // OK, function
 
})();</pre>

有一些方法可以绕过去，这里就不展开了，可以见<a href="http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/#codecalleecode-and-codecallercode-restrictions" target="_blank">http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/#codecalleecode-and-codecallercode-restrictions</a>。

## 重复命名

重复命名将会报错，包括属性名和参数名

<pre class="lang:js decode:true ">"use strict";
 
// SyntaxError
var foo = {
  x: 10,
  x: 10
};

// SyntaxError
function foo(x, x) {}</pre>

get和set方法在严格或非严格的模式下，都不能重名

<pre class="lang:js decode:true ">// strict or non-strict mode
 
// SyntaxError
var foo = {
  get x() {},
  get x() {}
};
 
// the same with setters
// SyntaxError
var bar = {
  set y(value) {},
  set y(value) {}
};

// SyntaxError
var foo = {
  x: 10,
  get x() {}
};</pre>

##  delete操作

实际上，变量、函数参数、函数都是无法被删除的，除非是在eval环境中，不过无法删除并不会报错。而在严格模式下，删除变量、函数参数、函数都会报错，包括在eval环境中删除也会报错。

<pre class="lang:js decode:true ">"use strict";
 
var foo = {};
 
function bar(x) {
  delete x; // SyntaxError
}
 
bar(10);  // SyntaxError
 
delete foo; // SyntaxError
delete bar; // SyntaxError
 
Object.defineProperty(foo, "baz", {
  value: 10,
  configurable: false
});
 
// but when delete a
// property, then TypeError
 
delete foo.baz; // TypeError
 
// SyntaxError
eval("var x = 10; delete x;"); // in non-strict is OK</pre>

## with语句

在严格模式下，不允许使用with语句

<pre class="lang:js decode:true ">"use strict";
 
// SyntaxError
with ({a: 10}) {
  alert(a);
}</pre>

## this值

在严格模式中，this值不再自动转换成一个对象， null和undefined的this值不再转换成全局对象，基本类型的值不再转换成包装对象，通过 <span class="lang:js decode:true  crayon-inline ">Function.prototype.apply</span> 和 <span class="lang:js decode:true  crayon-inline ">Function.prototype.call</span> 传入的this值不再转换成对象：

<pre class="lang:js decode:true ">"use strict";
 
// undefined "this" value,
// but not the global object
function foo() {
  alert(this); // undefined
}
 
foo(); // undefined
 
// "this" is a primitive
Number.prototype.test = function () {
  alert(typeof this); // number
};
 
1..test(); // number
 
foo.call(null); // null
foo.apply(undefined); // undefined</pre>

this被设置为undefined可以避免忘记使用new 来调用构造函数

<pre class="lang:js decode:true ">// non-strict
function A(x) {
  this.x = x;
}
 
var a = A(10); // forget "new" keyword
 
// as a result "a" is undefined,
// because exactly this value is returned
// implicitly from the A function
 
alert(a); // undefined
 
// and again created "x" property
// of the global object, because "this"
// is coerced to global object in the
// non-strict in such case
 
alert(x); // 10</pre>

在严格模式下会抛出异常

<pre class="lang:js decode:true ">"use strict";
 
function A(x) {
  this.x = x;
}
 
// forget "new" keyword,
// error, because undefined.x = 10
 
var a = A(10);
 
var b = new A(10); // OK</pre>

# 总结

可以看到，严格模式限制了Javascript现有的一些有风险的做法，减少了我们出错的几率。同时一些将来会废弃的用法，在严格模式中也禁用了，所以启用严格模式也能避免产生一些历史遗留代码。IE10+才支持严格模式，在低版本下会被忽略，而firefox、chrome和safari浏览器在比较早的版本就支持了，包括移动版。

本文固定链接：<http://blog.comiclee.com/?p=218>

# 参考资料

<a href="http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/" target="_blank">http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/</a>