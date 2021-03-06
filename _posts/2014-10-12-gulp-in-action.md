---
title: gulp实践
categories:
  - 前端工程化
tags:
  - gulp
---
# 什么是gulp

gulp是一种自动化构建工具，基于Node.js，主要用于前端模块的构建，包括对js、css（以及sass、less、CoffeeScript）的编译、打包、压缩、添加版本号等。

# 为什么使用gulp

前端构建工具最有名的当属grunt了，grunt有庞大的插件群来处理各种任务。它们对外暴露的接口是一堆的配置项，这是很方便的，但是当任务变得复杂，配置项变得越来越多时，这种配置的方式变得更加复杂，不太直观。

对于程序员来说，能够清晰看到程序的运行流程是更好的方式，gulp是一种流式的任务管理工具，可以看到任务中的对数据流的操作过程，这个对程序员来说更易于理解。<!--more-->

其它的诸如fis等集成解决方案太重了，一般都需要以一定的规范和框架组织代码，对模板语言也有限制，不够灵活。

# 开始使用gulp

1 在全局环境中安装gulp，以便于直接使用gulp命令 `npm install gulp -g`

2 安装gulp到当前项目中，使得可以通过require加载gulp模块。同时安装其它的几个需要的插件，下面举例使用gulp-rev-all插件来完成给静态资源打版本号的操作。这个插件并没有处理css中的图片，所以我们再使用一个gulp-rev-css-url插件来完成这个工作。

```
    npm install gulp --save
    npm install gulp-rev-all --save
    npm install gulp-rev-css-url --save
```

3 在gulp执行目录下创建一个gulpfile.js文件，把需要做的操作写在该文件中。

```
    var gulp = require('gulp');
    var revall = require('gulp-rev-all');
    var revcss = require('gulp-rev-css-url');
    gulp.task('revall', function () {
       return gulp.src([
         '../static/css/**/*.css',
         '../static/js/**/*.js',
         '../static/img/**/*.*',
         '../static/html/**/*.html',
         '../WEB-INF/views/**/*.ftl'
       ],{base:'../'})
       .pipe(revall({
         ignore: ['.xml', '.html', '.ftl'],
         transformPath: function (rev, source, path) {
           return rev.replace('/static', 'http://cdn.mycompany.com');
         }
       }))
       .pipe(gulp.dest('../'))
       .pipe(revcss())
       .pipe(gulp.dest('../'))
       .pipe(revall.manifest())
       .pipe(gulp.dest('../'));
    });
    gulp.task('default', ['revall']);
```

以上代码定义了一个叫revall的task，和default的task，我们执行`gulp revall`就可以直接运行revall的task。或者执行`gulp`直接运行默认的task

4 编写自己的task。本文先介绍使用普通的方式编写task，即非流式的形式，这很简单，能够方便的和现有的编译脚本整合：

```
    gulp.task('init',function() {
      require('./init.js');
    });
    gulp.task('revall', ['init'], function () {
      ...
    });
```

我们定义了一个叫init的task，在这个task中使用普通的Node语句来写，并没有使用gulp的流式处理的写法。同时，我们修改了revall的定义，使其依赖于init。

# gulp的执行顺序

上面内容定义了2个顺序执行的task。这里对gulp的执行顺序多说几句：

1. revall会等到所依赖的init task执行完毕后执行，如果它依赖多个task，如：[&#8216;init&#8217;, &#8216;task1&#8242;, &#8216;task2&#8242;, &#8230;]，则这些task会依次执行结束后再执行revall。由于这些task是异步执行的，所以task1开始执行时，init可能还未结束，如果要定义严格的执行顺序，则需要让task1依赖于init。

2. task代码执行结束时，数据流的操作不一定结束，这使得一个task执行时，它所依赖的task仍然还没有执行完，最简单的解决方法是在task定义的函数中return最后一个数据流对象。

#参考文章

1. <a href="https://github.com/gulpjs/gulp/blob/master/docs/README.md" target="_blank">gulp documentation</a>
2. <a title="Gulp：任务自动管理工具" href="http://javascript.ruanyifeng.com/tool/gulp.html" target="_blank">Gulp：任务自动管理工具</a>
3. <a title="前端工程化：在gulp中顺序执行任务" href="http://www.lifelaf.com/blog/?p=1210" target="_blank">前端工程化：在gulp中顺序执行任务</a>
