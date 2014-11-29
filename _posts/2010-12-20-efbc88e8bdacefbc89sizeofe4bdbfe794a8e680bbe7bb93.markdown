---
author: nifeng
comments: true
date: 2010-12-20 03:34:17+00:00
layout: post
slug: '%ef%bc%88%e8%bd%ac%ef%bc%89sizeof%e4%bd%bf%e7%94%a8%e6%80%bb%e7%bb%93'
title: sizeof使用总结（转）
wordpress_id: 49
categories: tech
tags: [C, C++, 转载]
---

同样是一道笔试题，可以看出，都是很基础的东西。主要考察我们在基础上的掌握深度。

sizeof C++/C的一个关键字：

#### sizeof定义 ####

>sizeof Operator
>
>sizeof expression
>
>The sizeof keyword gives the amount of storage, in bytes, associated with a variable or a type
>(including aggregate types). This keyword returns a value oftype size_t.（但是sizeof不是函数，它的结果是在编译阶段求值，也就是编译时常量）
>
>The expression is either an identifier or a type-cast expression (a type specifier enclosed in
>parentheses).
>
>When applied to a structure type or variable, sizeof returns the actual size, which may include
>padding bytes inserted for alignment. When applied to a statically dimensioned array, sizeof
>returns the size of the entire array. The sizeof operatorcannot return the size of dynamically
>allocated arrays or external arrays.

#### sizeof的语法形式 ####

1. sizeof(type name)

2. sizeof(expr);

3. sizeof expr;

#### sizeof用法实例 ####

**C++固有数据类型**

32位C++中的基本数据类型，也就`char,short int(short),int,long int(long),float,double, long double`
大小分别是：1，2，4，4，4，8, 10。

{% highlight c++ %}
cout<<sizeof(int)<<endl; //最基本用法，输出4

cout<<sizeof(sizeof(unsigned int) == sizeof(int))<<endl; // 相等，输出 1
{% endhighlight %}

分析以上代码最外层`sizeof()`内实际上相当与bool类型，即`sizeof(bool)`;

**指针类型**

{% highlight c++ %}
cout<<sizeof(string*)<<endl; // 4
cout<<sizeof(int*)<<endl; // 4
cout<<sizof(char****)<<endl; // 4
{% endhighlight %}

不管是什么类型的指针，大小都是4的，因为指针就是32位的物理地址。

结论：只要是指针，大小就是4（包括`void*`)。

**函数类型**
{% highlight c++ %}
int f1(){int a=0;return 0;};
double f2(){return 0.0;}
void f3(){}

void* f4(){return 0;}

cout<<sizeof(f1())<<endl; // f1()返回值为int，因此被认为是int
cout<<sizeof(f2())<<endl; // f2()返回值为double，因此被认为是double
cout<<sizeof(f3())<<endl; // 错误！无法对void类型使用sizeof

cout<<sizeof(f4())<<endl;//返回void*,不对指针进行解引用，因此被认为是void* ，输出4
cout<<sizeof(f1)<<endl; // 错误！无法对函数指针使用sizeof
cout<<sizeof*f2<<endl; // *f2，和f2()等价，因为可以看作object，所以括号不是必要的。被认为是double
{% endhighlight %}

**数组类型**

{% highlight c++ %}
char a[] = "abcdef";
int b[20] = {3, 4};
char c[2][3] = {"aa", "bb"};

cout<<sizeof(a)<<endl; // 7
cout<<sizeof(b)<<endl; // 20*4
cout<<sizeof(c)<<endl; // 6
{% endhighlight %}

数组a的大小在定义时未指定，编译时给它分配的空间是按照初始化的值确定的，也就是7。c是多维数组，占用的空间大小是各维数的乘积，也就是6。
可以看出，数组的大小就是他在编译时被分配的空间，也就是各维数的乘积\*数组元素的大小。

数组的大小是各维数的乘积\*数组元素的大小。

{% highlight c++ %}
int *d = new int[10];
cout<<sizeof(d)<<endl; // 4
{% endhighlight %}

在文章开头的定义里说sizeof不能对动态分配的变量求大小。`sizeof(d)`被看成是对`int*`求大小，因此输出指针大小，而不是`10*4`；

{% highlight c++ %}
double* (*a)[3][6]; //a是一个指向double*数组的指针
cout<<sizeof(a)<<endl; // 4是指针，所以sizeof(a)就是4。
cout<<sizeof(*a)<<endl; // 72，*a就表示一个double*[3][6]的多维数组类型，因此sizeof(*a)=3*6*sizeof(double*)=72
cout<<sizeof(**a)<<endl; // 24，**a表示一个double*[6]类型的数组，所以sizeof(**a)=6*sizeof(double*)=24
cout<<sizeof(***a)<<endl; // 4 。***a就表示其中的一个元素，也就是double*了，所以sizeof(***a)=4
cout<<sizeof(****a)<<endl; // 8 ****a，就是一个double了，所以sizeof(****a)=sizeof(double)=8
{% endhighlight %}

**结构体类型**

{% highlight c++ %}
struct A{
A(){}
~A(){}
int k,j;
};
struct B{
B(){}
~B(){}
int k;
char m;
static char m1;
};
struct C{
C(){}

char m1;
char m2;
};

cout <<sizeof(A)<<sizeof(B)<<sizeof(C)<<endl;
{% endhighlight %}

//答案：8，8，2

计算结构变量的大小就必须讨论数据对齐问题。为了CPU存取的速度最快（这同CPU取数操作有关，详细的介绍可以参考一些计算机原理方面的书），
C++在处理数据时经常把结构变量中的成员的大小按照4或8的倍数计算，这就叫数据对齐（data alignment）。

结构体(c++的类也是一样)字节对齐规则:
字节对齐的细节和编译器实现相关，但一般而言，满足三个准则：
1) 结构体变量的首地址能够被其最宽基本类型成员的大小所整除；
2) 结构体每个成员相对于结构体首地址的偏移量（offset）都是成员大小的整数倍，如有需要编译器会在成员之间加上填充字节（internal adding）；
3) 结构体的总大小为结构体最宽基本类型成员大小的整数倍，如有需要编译器会在最末一个成员之后加上填充字节（trailing padding）。 
如sizeof(B),其实际大小应该是6，自动补齐后char 各占两个字节，大小为8；

数据对齐是指数据所在的内存地址必须是该数据长度的整数倍，DWORD数据内存起始地址能被4除，WORD数据的起始地址能被2除。
x86CPU能直接访问对齐的数据，当它试图访问一个未对齐的数据时，会进行调整保证数据对齐。

陷阱：

1.  “空结构体”（不含数据成员）的大小不为0，而是1。试想一个“不占空间”的变量如何被取地址、两个不同的“空结构体”变量又如何得以区分呢？
    于是，“空结构体”变量也得被存储，这样编译器也就只能为其分配一个字节的空间用于占位了。如下：

    {% highlight c++ %}
    struct S5 { };
    sizeof( S5 ); // 结果为1
    {% endhighlight %}

2. \#pragram pack

用来调整结构体对齐方式的，不同编译器名称和用法略有不同.
\#pragma pack的基本用法为：\#pragma pack( n )，n为字节对齐数，其取值为1、2、4、8、16，
默认是8，如果这个值比结构体成员的sizeof值小，那么该成员的偏移量应该以此值为准，即是说，结构体成员的偏移量应该取二者的最小值，公式如下：

`offsetof( item ) = min( n, sizeof( item ) )`

搞清楚了以上所有内容，关于sizeof的基本知识也算有个完整的了解了

******************************************************************************************

******************************************************************************************
华丽分隔线。。。。

******************************************************************************************
1.  因为静态变量是存放在全局数据区的，而sizeof计算栈中分配的大小，所以静态变量是不会计算在内的。

2.  当类中存在虚函数时，编译器会为类加上一项成员变量，是一个指向该虚函数表的指针（通常称为vptr），
    所以这时使用sizeof时，需要加上这个变量的长度，即4个字节。
