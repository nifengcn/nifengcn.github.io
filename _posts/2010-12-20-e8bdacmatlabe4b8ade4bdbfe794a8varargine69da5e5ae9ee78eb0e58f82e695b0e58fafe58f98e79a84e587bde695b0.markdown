---
author: nifeng
comments: true
date: 2010-12-20 03:35:43+00:00
layout: post
slug: '%e8%bd%acmatlab%e4%b8%ad%e4%bd%bf%e7%94%a8varargin%e6%9d%a5%e5%ae%9e%e7%8e%b0%e5%8f%82%e6%95%b0%e5%8f%af%e5%8f%98%e7%9a%84%e5%87%bd%e6%95%b0'
title: Matlab中使用varargin来实现参数可变的函数(转)
wordpress_id: 53
categories: tech
ctags: matlab
---

原址：http://xialulee.spaces.live.com/Blog/cns!4EE324C8ACFA82DB!701.entry

在写[《用Matlab演示Parzen窗法》](http://xialulee.spaces.live.com/blog/cns%214EE324C8ACFA82DB%21675.entry)的时候，
我还不知道怎样在m中设计可变参数函数。

后来阅读一些m自带函数源码时发现了它的秘密，只要将最后一个参数设置为varargin，就可以实现。
例如，写了一个函数：`function test(varargin)`
那么在这个函数里，varargin就是一个cell数组，它包含了用户输入的参数。下面是一个例子：

{% highlight matlab %}
function test(varargin)
disp(['nargin is ' num2str(nargin)]);
for v=1:numel(varargin)
disp(['varargin{' num2str(v) '} class is ' class(varargin{v})]);
end
{% endhighlight %}

完全就是使用cell数组的方法来处理用户输入的个数不定的参数，如果我们这样调用这个函数：

`>> test()`

会得到结果：

`nargin is 0`

表示用户输入参数个数为0个，就是没有输入参数。

如果这样调用：

`>> test(1,'a',uint8(32),'123456');`

会得到结果：

{% highlight matlab %}
nargin is 4
varargin{1} class is double
varargin{2} class is char
varargin{3} class is uint8
varargin{4} class is char
{% endhighlight %}

表示用户输入了4个参数，第一个是double类型，第二个是char类型，第三个是uint8类型，第四个也是char类型。
也就是说，只要把函数的参数列表的最后一个参数写成varargin，就可以在函数内部以访问cell数组的方式来获取可变参数的信息。
