---
author: nifeng
comments: true
date: 2010-12-20 03:31:43+00:00
layout: post
slug: windows-sdk%e4%b8%ad%e7%aa%97%e5%8f%a3%e7%b1%bb%e4%b8%ad%e7%9a%84%e5%9b%be%e6%a0%87%e9%97%ae%e9%a2%98
title: windows SDK中窗口类中的图标问题
wordpress_id: 43
categories:
- tech
tags: [vc]
---

在窗口WNDCLASSEX中有两个图标，分别为：hIcon和hIconSm，前者是图标，后者是小图标。

图标hIcon指的是程序运行时在任务栏上的显示的图案。

小图标hIconSm指的是程序运行界面左上角的显示的图案。

当hIconSm为空时，系统会自动从hIcon中选择一个合适大小的图案来显示。

（注意：.icon文件是一组小位图的集合，而不仅仅是一个，每次显示时，系统会自动从中挑选一个最合适的来作为显示输出）

另外执行文件上的图标（也是XXXX.exe上面的图标）是由该项目资源中数值最小的图标决定的，
也就是说执行文件上的图标始终都是该资源中数值最小的图标，而与hIcon和hIconSm的取值无关。





