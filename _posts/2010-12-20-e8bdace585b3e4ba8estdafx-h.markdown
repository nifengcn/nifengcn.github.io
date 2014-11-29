---
author: nifeng
comments: true
date: 2010-12-20 03:29:11+00:00
layout: post
slug: '%e8%bd%ac%e5%85%b3%e4%ba%8estdafx-h'
title: 关于stdafx.h(转)
wordpress_id: 39
categories: tech
tags: [VC++, 转载]
---

所谓头文件预编译，就是把一个工程(Project)中使用的一些MFC标准头文件(如Windows.H、Afxwin.H)预先编译，以后该工程编译时，
不再编译这部分头文件，仅仅使用预编译的结果。这样可以加快编译速度，节省时间。

预编译头文件通过编译stdafx.cpp生成，以工程名命名，由于预编译的头文件的后缀是“pch”，所以编译结果文件是projectname.pch。

编译器通过一个头文件stdafx.h来使用预编译头文件。stdafx.h这个头文件名是可以在project的编译设置里指定的。
编译器认为，所有在指令#include "stdafx.h"前的代码都是预编译的，它跳过#include "stdafx. h"指令，
使用projectname.pch编译这条指令之后的所有代码。
因此，所有的MFC实现文件第一条语句都是：#include "stdafx.h"。

**详细工作原理及作用**

stdafx.h中没有函数库，只是定义了一些环境参数，使得编译出来的程序能在32位的操作系统环境下运行。
Windows和MFC的include文件都非常大，即使有一个快速的处理程序，编译程序也要花费相当长的时间来完成工作。
由于每个.CPP文件都包含相同的include文件，为每个.CPP文件都重复处理这些文件就显得很傻了。

为避免这种浪费，AppWizard和VisualC++编译程序一起进行工作，如下所示：

>◎AppWizard建立了文件stdafx.h，该文件包含了所有当前工程文件需要的MFCinclude文件。且这一文件可以随被选择的选项而变化。
>
>◎AppWizard然后就建立stdafx.cpp。这个文件通常都是一样的。
>
>◎然后AppWizard就建立起工程文件，这样第一个被编译的文件就是stdafx.cpp。
>
>◎当VisualC++编译stdafx.cpp文件时，它将结果保存在一个名为stdafx.pch的文件里。(扩展名pch表示预编译头文件。)
>
>◎当VisualC++编译随后的每个.cpp文件时，它阅读并使用它刚生成的.pch文件。VisualC++不再分析Windowsinclude文件，除非你又编辑了stdafx.cpp或stdafx.h。


这个技术很精巧，你不这么认为吗？(还要说一句，Microsoft并非是首先采用这种技术的公司，Borland才是。)在这个过程中你必须遵守以下规则：

>◎你编写的任何.cpp文件都必须首先包含stdafx.h。
>
>◎如果你有工程文件里的大多数.cpp文件需要.h文件，顺便将它们加在stdafx.h(后部)上，然后预编译stdafx.cpp。
>
>◎由于.pch文件具有大量的符号信息，它是你的工程文件里最大的文件。
>如果你的磁盘空间有限，你就希望能将这个你从没使用过的工程文件中的.pch文件删除。
>执行程序时并不需要它们，且随着工程文件的重新建立，它们也自动地重新建立。

**如何理解其原理**

通俗一点说当我们使用AppWizard来自动生成某些项目的时候,系统会自动把所需要include的头文件在stdafx.h中先include一下,
这样,我们只需要直接include这个stdafx.h文件即可.因为同一个项目中的不同源文件CPP都包含相同的include文件,
这样,为每个.CPP文件都重复include这些文件就显得很傻了。当然如果你不用MFC的话就不用了。
即：在每个.cpp文件中都include stdafx.h 就相当于包含了其他的如iostream.h等文件

**与stdio.h的区别**

我们一般用TC或vc编译C程序的时候都要首先包含这个stdio.h头文件，这个头文件里面包含了scanf和printf函数的定义，
如果我们不在程序开头include这个文件，那么你调用上面这两个函数就不会成功，它其实和c++中的iostream.h文件的作用差不多的，
它们一般都已经在stdafx.h文件中被包含。
