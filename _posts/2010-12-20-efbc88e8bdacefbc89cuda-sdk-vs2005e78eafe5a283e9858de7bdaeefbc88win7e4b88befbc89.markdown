---
author: nifeng
comments: true
date: 2010-12-20 02:34:38+00:00
layout: post
slug: '%ef%bc%88%e8%bd%ac%ef%bc%89cuda-sdk-vs2005%e7%8e%af%e5%a2%83%e9%85%8d%e7%bd%ae%ef%bc%88win7%e4%b8%8b%ef%bc%89'
title: CUDA SDK vs2005环境配置（win7下）（转）
wordpress_id: 20
categories: tech
tags: [CUDA, 转载]
---

1.  下载OpenHero的CUDA VS Wizard 2.2 beta，然后可以直接在vs工程--New看到相应的图标如下：
    该版本增加对cuda3.0 rule的支持，并接可以直接添加.cu文件
    [http://blog.csdn.net/OpenHero/archive/2010/01/06/5146398.aspx](http://blog.csdn.net/OpenHero/archive/2010/01/06/5146398.aspx)
    ![](http://hiphotos.baidu.com/forrestyx/pic/item/894b54def0a03d2f63279896.jpg)

2.  **系统环境变量的设置**.虽然有了hero的 wizard，还不完整~~但是环境变量等东西还是需要自己设置，否则无法正常运行cuda程序。
    一项一项来设置吧：

    * CUDA_INC_PATH   cuda安装的包含目录和cuda sdk安装的包含目录 （win7下xp下默认的安装路径是不同的）如：
    `D:\CUDA\include;H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\common\inc`

    * CUDA_LIB_PATH
    `D:\CUDA\lib;H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\common\lib`

    * CUDA_BIN_PATH
    `D:\CUDA\bin`

    * 这里如果直接将SDK的bin（dll库）路径添加在cuda_bin_path会出问题，可能是因为我的路径里面包含空格
    （大概是这样，gg了好久，没有弄明白这个问题，注：这都是使用hero的wizard出现的问题，我用template时没有该问题，看来是否应该请教下hero，
    可是貌似我不认识他yeah），所以开始一直出错。

    H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\bin\win32\Debug;
    H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\bin\win32\EmuDebug;
    H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\bin\win32\EmuRelease;
    H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\bin\win32\Release

    后来我将其添加到PATH路径里（这项在vs2005 vc directory下已经设置好了，不用理会），成功

3.  VAssistX注册表的修改

    regedit --进入注册表 HKEY_CURRENT_USER---SoftWare---Whole Tomato---Visual AssistX--VANet8

    右边栏找到**ExtSource增加数据值：.cu;.cuh;**

4.  如果对的cuda规则不确定，请你重新添加build规则（如果安装了新版本的cuda sdk，这项也必不可少），
    否则会出现**final nvcc.exe error.** **A single input file is required for a non-link phase when an output file is**** specified** (wizard对“CUDA_INC_PATH”里的" "不识别引起的)大致说是command line路径有问题，CSDN里有人说可以如下解决：（1）一般的解决的方法就是就是quote cuda-general-> ad include dir 后面的路径；（2）第二种就是将cuda装在一级目录下。从我遇到的问题分析来看应该是cuda rules不匹配，因为之前安装了较低版本的cuda,现在使用的是3.0的ruler，没有覆盖掉。解决：

    在project上右键---custom build rule files--添加新安装的cuda rule文件所在目录，如：

    **H:\ProgramData\NVIDIA Corporation\NVIDIA GPU Computing SDK\C\common\cuda.rules**

    按部就班完整以上步骤，环境配置基本完成。**如果在调试过程中还有bug，可能是某些头文件、库文件没有包含，找到相应路径添加进来。 OK！！**

    贴个图：

    ![](http://hiphotos.baidu.com/forrestyx/pic/item/a8a1e2ee76d9212afcfa3c0c.jpg)

    如果出现如下错误（使用的是openhero的vs wizard，不是使用cuda自带的template)：

{% highlight c++ %}
: error LNK2005: "void __cdecl __device_stub__Z10testKernelPfS_(float *,float *)" ([?__device_stub__Z10testKernelPfS_@@YAXPAM0@Z](mailto:?__device_stub__Z10testKernelPfS_@@YAXPAM0@Z)) already defined in FirstTest.cu.obj
error LNK2005: "void __cdecl testKernel(float *,float *)" ([?testKernel@@YAXPAM0@Z](mailto:?testKernel@@YAXPAM0@Z)) already defined in FirstTest.cu.obj
{% endhighlight %}

在kernel函数声明前前缺少static,正确应该如下**：__global__ static void (保证该函数只能在本文件中的函数所调用，不能被其他文件所调用） 因此不会出现以上重定义现象**

至少我是这么理解的，不知道openhero在写这个wizard的时候是怎么回事,因为模板里调用就不存在该问题……

如果用template不存在该问题~~
