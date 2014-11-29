---
author: nifeng
comments: true
date: 2010-12-20 02:38:45+00:00
layout: post
slug: '%ef%bc%88%e8%bd%ac%ef%bc%89wchar_t%e7%b1%bb%e5%9e%8b%e7%9a%84%e5%ae%9e%e8%b4%a8'
title: wchar_t类型的实质（转）
wordpress_id: 22
categories: tech
tags: [VC++, 转载]
---

VC++编译器中包括一种本机类型，用来支持长字符串，它就是wchar_t，它所占用的内存长度为2个字节，
同时VC++编译器2个字节长度的类型有short, unsigned short。[参考链接][link]。
它们相互之间其实是可以进行类型转换而不会发生内存定位错误的，如：

（在打开编译器选项/Zc:wchar_t的情况下）
{% highlight c++ %}
#include "stdafx.h"
#include <windows.h>

int _tmain(int argc, _TCHAR* argv[]) {
    unsigned short *pUShort;    //或是 short *pUShort;
    wchar_t *pWChar = L"ABCD";
    pUShort = (unsigned short*)pWChar;
    MessageBoxW(NULL,(wchar_t*)pUShort, L"ok", 0); //OK,这样是可以的
    return 0;
}
{% endhighlight %}

所以，VC++对wchar_t提供了两种实现方式，

1.以unsinged char 来做为`wchar_t`来处理：`typedef unsigned char wchar_t ;`

2.直接提供wchar_t的本机类型。它由加入/Zc:wchar_t编译器选项来实现。

为什么要这样做？

它可能是与在X386平台下的int和long类型的关系一样，它们的内存空间长度是相同的，但明显long这个名称可以给人一种直观的认识。
那么这个编译器选项到底做了什么呢？

如下函数：

`public: virtual long __stdcall CBaseFilter::QueryVendorInfo(wchar_t * *)`

在没有加入/Zc:wchar_t编译器选项时，编译器输出的函数符号为：

`[?QueryVendorInfo@CBaseFilter@@UAGJPAPAG@Z](mailto:?QueryVendorInfo@CBaseFilter@@UAGJPAPAG@Z)`

但在加入此选项之后，编译器输出的函数符号为：

`[?QueryVendorInfo@CBaseFilter@@UAGJPAPA_W@Z](mailto:?QueryVendorInfo@CBaseFilter@@UAGJPAPA_W@Z)`

看到了吗？最后的一个字符是不同的，G（未加选项）_W(加选项）

所以它们的最终输出的函数符号是不同的，这样的话，会发生某些情况：

如果生成了一个静态或是动态库，它使用unsigned char来实现wchar_t类型，而调用它的程序以本机类型方式来实现`wchar_t`类型，
则在链接调用程序时，会发生“unresolved external symbol ”（“无法解析的外部符号”）错误。
所以在使用wchar_t类型时一定要注意多个工程相互调用时，要将wchar_t的实现方式设置为相同的。

实际工作中，可能会经常出现的由此原因所产生的错误：

directshow所提供的BaseClasses工程就默认将`wchar_t`设置为由unsigned short来实现，
如果由MFC程序来实现一个direct show工程的话，因为MFC工程默认将提供wchar_t本机类型。
所以如果直接编译的话，在最后的链接过程之中就会发生以下的链接错误：

{% highlight c++ %}
MP4MiscRenderer.obj : error LNK2001: 无法解析的外部符号 "public: virtual long __stdcall CBaseFilter::QueryVendorInfo(wchar_t * *)" ([?QueryVendorInfo@CBaseFilter@@UAGJPAPA_W@Z](mailto:?QueryVendorInfo@CBaseFilter@@UAGJPAPA_W@Z))
MP4MiscRenderer.obj : error LNK2001: 无法解析的外部符号 "public: virtual long __stdcall CBaseFilter::JoinFilterGraph(struct IFilterGraph *,wchar_t const *)" ([?JoinFilterGraph@CBaseFilter@@UAGJPAUIFilterGraph@@PB_W@Z](mailto:?JoinFilterGraph@CBaseFilter@@UAGJPAUIFilterGraph@@PB_W@Z))
MP4MiscRenderer.obj : error LNK2001: 无法解析的外部符号 "public: virtual long __stdcall CBaseRenderer::FindPin(wchar_t const *,struct IPin * *)" ([?FindPin@CBaseRenderer@@UAGJPB_WPAPAUIPin@@@Z](mailto:?FindPin@CBaseRenderer@@UAGJPB_WPAPAUIPin@@@Z))
{% endhighlight %}

这是一个很不明显的错误，单从源代码的角度来看，是无法找出错误的位置的。



[link]:<http://msdn.microsoft.com/zh-cn/library/dh8che7s%28VS.80%29.aspx>
