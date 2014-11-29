---
author: nifeng
comments: true
date: 2010-12-20 03:32:30+00:00
layout: post
slug: vs2008%e4%b8%adcaption%e5%b1%9e%e6%80%a7%e5%a6%82%e4%bd%95%e8%ae%be%e7%bd%ae%e4%b8%ba%e4%b8%ad%e6%96%87
title: vs2008中caption属性如何设置为中文
wordpress_id: 45
categories: tech
tags: VC++
---

vs2008对资源文件的支持默认是英文，如果想要在caption属性中添加中文，则需要在工程的属性页中进行设置，设置如下：

在工程的属性页中，选择configuration properties其中的resource,点击其中的general,然后将右侧窗中的culture设置为简体中文。

截图如下：![](http://hiphotos.baidu.com/%BB%A8%C7%E1_%CB%C6%C3%CE/pic/item/901a3cdd6a8788a877c63869.jpg)
