---
author: nifeng
comments: true
date: 2010-12-20 02:26:37+00:00
layout: post
slug: '%ef%bc%88%e8%bd%ac%ef%bc%89%e9%93%be%e6%8e%a5%e6%8c%87%e7%a4%ba%ef%bc%9a-extern-c-c%e5%92%8cc%e6%b7%b7%e5%90%88%e7%bc%96%e7%a8%8b%e9%97%ae%e9%a2%98'
title: 链接指示： extern "C"  C和C++混合编程问题(转)
wordpress_id: 17
categories: tech
tags: [C, C++, 转载]
---

分析以下一段代码：

{% highlight c++%}
/*=======sum.h=========*/

#ifndef SUM_H
#define SUM_H
#include <stdio.h>

int sum(int a,int b);
#endif;

/*=======sum.c=========*/

#include "sum.h"
int sum(int a,int b)
{
int c=a+b;
return c;
}

/*====main.cpp======*/

#include "sum.h"

void mian(){

cout << sum(1,2)<<endl;

}
{% endhighlight %}

调用以上三个文件，编译通过，但是执行是出现以下问题：

{% highlight c++%}
obj : error LNK2001: 无法解析的外部符号 "int __cdecl sum(int,int)"
E:\Programming\Grapic\test\Debug\test.exe : fatal error LNK1120: 1 个无法解析的外部命令
{% endhighlight %}

问题出在哪里呢？ 在main.cpp里调用了sum.c，也就是说在C++程序里调用了C程序，此时如果没有作相应处理将会出现链接错误。

extern "C"表示编译生成的内部符号名使用C约定。C++支持函数重载，而C不支持，两者的编译规则也不一样。
函数被C++编译后在符号库中的名字与C语言的不同。例如，假设某个函数的原型为：`void foo( int x, int y );`
该函数被C编译器编译后在符号库中的名字可能为_foo，
而C++编译器则会产生像_foo_int_int之类的名字（不同的编译器可能生成的名字不同，但是都采用了相同的机制，生成的新名字称为“mangled name”）。
_foo_int_int这样的名字包含了函数名、函数参数数量及类型信息，C++就是靠这种机制来实现函数重载的。

那么如果在C中调用C++代码，以及如何在C++中调用C的代码呢？

extern "C"表示编译生成的内部符号名使用C约定。

#### 如何在C++中调用C呢？####

C++调用C，extern "C" 的作用是：让C++连接器找调用函数的符号时采用C的方式

本文开头提出的笔试题可以这样修改：

{% highlight c++%}
/*=======sum.h=========*/

#ifndef SUM_H
#define SUM_H
#include <stdio.h>

int sum(int a,int b);
#endif;

/*=======sum.c=========*/

#include "sum.h"
int sum(int a,int b)
{
int c=a+b;
return c;
}

/*====main.cpp======*/

extern "C"
{
#include "sum.h"
}

void mian(){

cout << sum(1,2)<<endl;

}
{% endhighlight %}

执行成功

相信到这里差不多明白了

#### 怎样在C里调用C++呢？ ####

    在C中引用C++函数(C调用C++，使用extern "C"则是告诉编译器把cpp文件中extern "C"定义的函数依照C的方式来编译封装接口，当然接口函数里面的C++语法还是按C++方式编译)

执行：**test1.obj : error LNK2019: 无法解析的外部符号 _sum，该符号在函数 _main 中被引用
E:\Programming\Grapic\test\Debug\test.exe : fatal error LNK1120: 1 个无法解析的外部命令**

{% highlight c++%}
/*=======sum.h=========*/

#ifndef SUM_H
#define SUM_H
#include <stdio.h>

int sum(int a,int b);
#endif;

/*=======sum.cpp=========*/

#include "sum.h"
extern "C"
{
    int sum(int a,int b)
    {
        int c=a+b;
        return c;
    }
}

/*====main.c======*/

#include "sum.h"

void mian(){
    cout << sum(1,2)<<endl;
}
{% endhighlight %}

#### 标准规范写法 ####

一般我们都将函数声明放在头文件，当我们的函数有可能被C或C++使用时，我们无法确定被谁调用，使得不能确定是否要将函数声明在extern "C"里，所以，我们可以添加

{% highlight c++%}
#ifdef __cplusplus

extern "C"

{

#endif

//函数声明

#ifdef __cplusplus

}

#endif
{% endhighlight %}

利用以上声明形式就可以综合运用了。

在C中引用C++语言中的函数和变量时，C++的函数或变量要声明在extern "C"{}里，但是在C语言中不能使用extern "C"，否则编译出错。
(出现错误: error C2059: syntax error : 'string',这个错误在网上找了很久，国内网站没有搜到直接说明原因的，
原因是extern "C"是C++中的关键词，不是C的，所有会出错。

{% highlight c++%}
/*=======sum.h=========*/

#ifndef SUM_H
#define SUM_H
#include <stdio.h>

int sum(int a,int b);
#endif;

/*=======sum.cpp=========*/

#include "sum.h"
int sum(int a,int b)
{
int c=a+b;
return c;
}

/*====main.c======*/

#include "sum.h"


void mian(){

cout << sum(1,2)<<endl;

}
{% endhighlight %}
