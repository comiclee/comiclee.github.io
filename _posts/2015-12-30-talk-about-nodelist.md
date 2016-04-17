---
date: 2015-12-30 15:49:04
categories: javascript
tags: javascript
title: 浅谈NodeList
---
在Javascript中有一些看起来像数组的变量，比如函数内的`arguments`变量、`getElementsByTagName()`的返回值等，它们有length属性，也可以通过`arguments[0]`的形式读取其中的某一项，但它们并不是数组，并没有push、pop等数组所具有的方法。<!--more-->
`getElementsByTagName()`的返回值是NodeList类型的对象，此外，文档节点的`childNodes`属性也是NodeList类型。除了不是数组以外，它们还具有特殊的性质——它们是动态的，对文档的任何改变都会实时反映到NodeList中。下面举个例子说明一下：

```
    <ul id="ul">
      <li>1</li>
      <li>2</li>
      <li>3</li>
      <li>4</li>
      <li>5</li>
    </ul>
    <script>
    var ul = document.getElementById('ul');
    var list = document.getElementsByTagName('li');
    for (var i=0; i<list.length; i++) {
        ul.appendChild(list[i]);
    }
    </script>
```

看看运行结果是什么

- 2
- 4
- 1
- 5
- 3

这个结果是不是有点出乎意料？但是结合上面描述的NodeList的特性就不难理解，由于list是动态的，所以每次循环都改变了列表，过程如下：

i=  | list顺序    
----| ----
0   | ***1*** 2 3 4 5
1   | 2 ***3*** 4 5 1
2   | 2 4 ***5*** 1 3
3   | 2 4 1 ***3*** 5
4   | 2 4 1 5 ***3***

由于NodeList是动态的特性，常常会产生出乎意料的结果，所以我们使用前可以将它转成普通的数组：

``` Javascript
    list = Array.prototype.slice.call(list);
```

此外，我们可以使用`querySelectorAll()`来代替`getElementsByTagName()`，`querySelectorAll()`返回值也是NodeList类型，但却是静态的NodeList，不会随着文档的变化而改变。如可以把代码改成：

``` Javascript
    var list = document.querySelectorAll('li');
```

这样改写后就能得到我们想要的结果：

- 1
- 2
- 3
- 4
- 5

另一种行为很类似的数据类型是HTMLCollection，document的许多属性都是HTMLCollection对象，例如document.all, document.forms, document.images, document.links, document.anchors等，还有Form.elements, Select.options等，它们也是动态的，与NodeList不同的是，HTMLCollection对象是只读的，不能给它添加新元素。
