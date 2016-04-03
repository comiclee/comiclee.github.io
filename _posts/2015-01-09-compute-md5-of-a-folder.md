---
title: 计算一个目录的md5值
categories:
  - Linux
tags:
  - Linux
---
使用Linux的md5sum命令可以计算出一个文件的md5值，这可以用于判断文件是否经过更改，使用方法如下：

<pre>md5sum filename</pre>

但md5sum命令无法作用于一个目录，那如何计算一个目录的md5呢？有一个思路是计算目录内所有文件的md5，然后再进行一次的md5计算。我们可以遍历目录，对所有文件进行计算：<!--more-->

<pre>sum=''
process() {
  local dir=$1
  for file in `ls $dir` ; do
    local path=$dir/$file
    if [ -d $path ] ; then
      process $path
    elif [ -f $path ] ; then
      sum=$sum$(md5sum $path)
    fi
  done
}
process pathname
echo $sum | md5sum</pre>

其实利用Linux的find命令，能够更方便快捷地达成目的

<pre>find pathname -type f | xargs md5sum | md5sum</pre>

当然，用这两种方法计算的md5是不一样的，要保证使用同一个md5算法，以免出问题。

&nbsp;
