---
author: nifeng
comments: true
date: 2012-08-18 05:47:21+00:00
layout: post
slug: bash-shell%e9%82%a3%e4%ba%9b%e4%ba%8b
title: bash shell那些事
wordpress_id: 208
categories:
- tech
tags:
- linux
- shell
---

shell是linux入门必备，为我们管理linux提供了非常大的帮助，
但是shell中也包含着一些非常奇怪的设计，即与我们通常的编程语言不一样的地方，需要我们在使用中注意的。

* ###||和&&###

在普通的编程语言中，||和&&是存在优先级的，可以从下面的例子看出来：

php的一个简单的程序：
{% highlight php %}
<?php
if (true || true && false) {
    echo 'condition 1 satified' . PHP_EOL;
}
if (true || true && true) {
    echo 'condition 2 satified' . PHP_EOL;
}
{% endhighlight %}

执行结果如下：
{% highlight php %}
condition 1 satified
condition 2 satified
{% endhighlight %}

由于||左边一直为true，所以||右边的内容就不用进行判断，所以整个判断条件总是为true。

再来看下shell中一个简单的脚本：
{% highlight bash %}
#!/bin/bash
test1='haha'
test2='heihei'
if [ $test1 = 'haha' ] || [ $test2 = 'heihei' ] && [ $test1 = 'haha' ]; then
    echo 'condition 1 satified'
fi
if [ $test1 = 'haha' ] || [ $test2 = 'heihei' ] && [ $test1 = 'haha1' ]; then
    echo 'condition 2 satified'
fi
{% endhighlight %}

执行结果如下：
{% highlight bash %}
condition 1 satified
{% endhighlight %}

整个逻辑和php程序中一样，但是得到的结果却不一样，在linux下面的命令都是从左到右执行的。

在condition1中，首先判断$test1是否等于'haha'，返回true(实际上是通过$?是否等于0来进行判断的)，
接下看到的等号是||，由于$?等于0，所以$test2是否等于'heihei'这个命令就不会执行，$?==0这个结果会继续向后传，来到&&这边，
由于$?==0，所以执行$test1是否等于'haha'时，执行结果为$?==0，所以condition1 satified会显示出来。
在contidion2中，前面的过程中都一样，但在执行最后的判断$test1是否等于'haha1'时，执行结果为$?!=0，所以condition2 satified就没有显示出来。

这一点差别在写程序的时候需要注意，至于千万这种结果的更底层的东西等有时间再挖掘下。^_^

