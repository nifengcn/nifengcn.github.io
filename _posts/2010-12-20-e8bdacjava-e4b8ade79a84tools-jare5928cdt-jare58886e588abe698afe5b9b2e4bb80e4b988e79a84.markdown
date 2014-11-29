---
author: nifeng
comments: true
date: 2010-12-20 03:38:37+00:00
layout: post
slug: '%e8%bd%acjava-%e4%b8%ad%e7%9a%84tools-jar%e5%92%8cdt-jar%e5%88%86%e5%88%ab%e6%98%af%e5%b9%b2%e4%bb%80%e4%b9%88%e7%9a%84'
title: java  中的tools.jar和dt.jar分别是干什么的(转)
wordpress_id: 59
categories: tech
tags: [JAVA, 转载]
---

rt.jar是JAVA基础类库，dt.jar是关于运行环境的类库，tools.jar是工具类库。
设置在classpath里是为了让你 import \*，web系统都用到tool.jar

1.  rt.jar默认就在 根classloader的加载路径里面 放在claspath是多此一举,不信你可以去掉classpath里面的rt.jar。
    然后用 java -verbose XXXX 的方式运行一个简单的类 就知道 JVM的系统根Loader的路径里面。
    不光rt.jar jre\lib下面的大部分jar 都在这个路径里

2.  tools.jar是系统用来编译一个类的时候用到的,也就是javac的时候用到javac XXX.java。
    实际上就是运行
    `java -Calsspath=%JAVA_HOME%\lib\tools.jar xx.xxx.Main XXX.java`。
    javac就是对上面命令的封装,所以tools.jar 也不用加到classpath里面

3.  dt.jar是关于运行环境的类库,主要是swing的包,你要用到swing时最好加上
