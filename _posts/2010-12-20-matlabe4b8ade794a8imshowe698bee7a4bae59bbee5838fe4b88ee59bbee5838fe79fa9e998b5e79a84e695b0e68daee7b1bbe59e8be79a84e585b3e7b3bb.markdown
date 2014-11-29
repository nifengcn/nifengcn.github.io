---
author: nifeng
comments: true
date: 2010-12-20 02:46:54+00:00
layout: post
slug: matlab%e4%b8%ad%e7%94%a8imshow%e6%98%be%e7%a4%ba%e5%9b%be%e5%83%8f%e4%b8%8e%e5%9b%be%e5%83%8f%e7%9f%a9%e9%98%b5%e7%9a%84%e6%95%b0%e6%8d%ae%e7%b1%bb%e5%9e%8b%e7%9a%84%e5%85%b3%e7%b3%bb
title: matlab中用imshow()显示图像与图像矩阵的数据类型的关系
wordpress_id: 30
categories: tech
tags: matlab
---

在matlab中，我们常使用imshow()函数来显示图像，而此时的图像矩阵可能经过了某种运算。
在matlab中，为了保证精度，经过了运算的图像矩阵I其数据类型会从unit8型变成double型。
如果直接运行imshow(I)，我们会发现显示的是一个白色的图像。
这是因为imshow()显示图像时对double型是认为在0~1范围内，即大于1时都是显示为白色，而imshow显示uint8型时是0~255范围。
而经过运算的范围在0-255之间的double型数据就被不正常得显示为白色图像了。
