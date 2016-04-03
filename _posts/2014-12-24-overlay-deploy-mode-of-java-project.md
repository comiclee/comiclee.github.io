---
title: java工程的overlay部署模式
categories:
  - Java
---
<p id="id-前端模块在java工程间的复用-背景" style="color: #333333;">
  前端模块化后，一个模块可以在多个页面或者一个页面的多处被复用，提升了我们的开发效率，避免重复造轮子。而后，我们对复用性有了更高的要求，能不能让一个模块在多个工程（网站）间复用呢？我们的几个网站大多情况下使用的是同一套UI，我们希望尽量避免把相同的代码从这个网站拷贝到另一个网站，而是使其成为更加公共的模块，能够在不同的网站间复用。
</p>

<p style="color: #333333;">
  maven的overlay模式能够将一个外部的war包部署到当前war包中，包括静态文件和class，最后的效果就是将两个war包合并起来了。例如我们的主模块使用了一个公共模块fe_common，按照如下方式配置overlay模式：
</p>

<!--more-->

  1. 在主模块的pom文件里加上依赖项 将依赖的模块加入dependency节点，并指明type值为war

<pre>&lt;dependency&gt;
   &lt;groupId&gt;com.company&lt;/groupId&gt;
   &lt;artifactId&gt;fe_common&lt;/artifactId&gt;
   &lt;version&gt;1.0.0-SNAPSHOT&lt;/version&gt;
   &lt;type&gt;war&lt;/type&gt;
 &lt;/dependency&gt;</pre>

<p style="color: #333333;">
  2. 添加一个plugin——maven-war-plugin，并在overlay中设置依赖的模块，以及包含的文件
</p>

<pre style="color: #333333;">&lt;plugin&gt;
   &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
   &lt;artifactId&gt;maven-war-plugin&lt;/artifactId&gt;
   &lt;version&gt;2.1.1&lt;/version&gt;
   &lt;configuration&gt;
     &lt;dependentWarIncludes&gt;WEB-INF/**/*,static/**/*&lt;/dependentWarIncludes&gt;
     &lt;overlays&gt;
       &lt;overlay&gt;
         &lt;groupId&gt;com.company&lt;/groupId&gt;
         &lt;artifactId&gt;fe_common&lt;/artifactId&gt;
       &lt;/overlay&gt;
     &lt;/overlays&gt;
   &lt;/configuration&gt;
 &lt;/plugin&gt;</pre>

<p style="color: #333333;">
  经过这两步就设置好了，在编译的时候，主模块会自动下载fe_common模块的war包，在打包阶段将两个war包合并一块儿打包。
</p>

<h1 id="id-前端模块在java工程间的复用-注意和说明" style="color: #333333;">
  注意和说明
</h1>

<p style="color: #333333;">
  1. 由于overlay模式是将依赖包的文件合并到当前包中，所以要避免文件冲突，最好把依赖包的文件放在一个特定的目录中，如主模块的目录结构是/static/css , /static/js , /WEB-INF/views ，而fe_common的目录结构是/static/css/fe_common ,  /static/js/fe_common , /WEB-INF/views/fe_common。由于在本地开发的时候，IDE或者maven会在源代码处添加一些临时目录，而且不会删掉，所以应在.gitignore文件中添加上排除的目录
</p>

<pre class="lang:sh decode:true">overlays
src/main/webapp/**/fe_common</pre>

<p style="color: #333333;">
  2. overlay形式的依赖能够正确的编译打包和部署，如上所述，最后两个模块会合并到一个包里。如果是在本地调试阶段，不少IDE能够正确识别overlay的模块并引入依赖。但如果是用普通编辑器+命令行编译，则更改了fe_common后应该mvn install一下，这样对主模块执行mvn compile时才能正确取到包
</p>

<p style="color: #333333;">
  3. 以nested方式运行jetty(mvn jetty:run)也可以识别出overlay模块，不过需要比较新的jetty plugin（最好升级到最新），但这种方式仍然不支持include ftl文件，猜测这和freemarker编译器的处理行为有关。解决方法是在maven的test-compile阶段把依赖的模块的ftl文件拷到本地，以使其正常运行。当然，这些拷过来的文件也不会被删掉，只要按照第1点所说的，在.gitignore文件中添加排除项就不会被加入到版本库中。
</p>

<pre class="lang:xhtml decode:true">&lt;plugin&gt;
  &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
  &lt;artifactId&gt;maven-dependency-plugin&lt;/artifactId&gt;
  &lt;version&gt;2.10&lt;/version&gt;
  &lt;executions&gt;
    &lt;execution&gt;
      &lt;id&gt;unpack&lt;/id&gt;
      &lt;phase&gt;test-compile&lt;/phase&gt;
      &lt;goals&gt;
        &lt;goal&gt;unpack&lt;/goal&gt;
      &lt;/goals&gt;
      &lt;configuration&gt;
        &lt;artifactItems&gt;
          &lt;artifactItem&gt;
            &lt;groupId&gt;com.company&lt;/groupId&gt;
            &lt;artifactId&gt;fe_common&lt;/artifactId&gt;
            &lt;version&gt;1.0.0-SNAPSHOT&lt;/version&gt;
            &lt;type&gt;war&lt;/type&gt;
            &lt;overWrite&gt;true&lt;/overWrite&gt;
            &lt;outputDirectory&gt;${basedir}/src/main/webapp&lt;/outputDirectory&gt;
            &lt;includes&gt;WEB-INF/views/**/*&lt;/includes&gt;
          &lt;/artifactItem&gt;
        &lt;/artifactItems&gt;
      &lt;/configuration&gt;
    &lt;/execution&gt;
  &lt;/executions&gt;
&lt;/plugin&gt;</pre>

参考资料

<p style="color: #333333;">
  <span style="color: #042eee;"><span style="text-decoration: underline;"><a class="external-link" style="color: #3b73af;" href="http://maven.apache.org/plugins/maven-war-plugin/overlays.html" target="_blank" rel="nofollow">http://maven.apache.org/plugins/maven-war-plugin/overlays.html</a></span></span> <span style="color: #042eee;"><span style="text-decoration: underline;"><a class="external-link" style="color: #3b73af;" href="http://eclipse.org/jetty/documentation/current/jetty-maven-plugin.html#using-overlaid-wars" target="_blank" rel="nofollow">http://eclipse.org/jetty/documentation/current/jetty-maven-plugin.html#using-overlaid-wars</a></span></span>
</p>
