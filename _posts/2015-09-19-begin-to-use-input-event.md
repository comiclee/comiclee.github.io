---
date: 2015-09-19 15:49:04
title: 开始使用input事件
---
在pc时代，我们习惯于使用keyup或keydow事件来实时监听输入框的输入，但在移动设备上可能会有些问题，例如最近在项目中发现，iphone的搜狗输入法输入时并没有触发keyup或keydown事件，导致对这两个事件的监听失败。<!--more-->

最简单的解决方法就是使用input事件。input事件是HTML5的标准事件，虽然在IE只有IE9以上才支持，但在移动设备上还是支持得挺好的，代码就不写了，直接把keyup/keydown事件替换成input事件即可。
