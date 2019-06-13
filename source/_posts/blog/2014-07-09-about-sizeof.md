---
title: 关于sizeof
date: 2014-07-09
categories: 技术
tags: ["c语言", "内存"]
---
 
 
 前两天犯了一个愚蠢的错误，由于对sizeof理解的错误，导致代码出现了一个bug,具体代码逻辑如下：
 

    char buf[512];
    char *ptr;
    int size;
    
    ptr = buf;
    size = sizeof(ptr);
    
本来是计算buf的大小，但这样写其实只是得到 char指针类型的大小，错误的以为ptr指向buf时，用sizeof获取的也是buf的大小。通过了解，如果sizeof的操作数为指针时，不管指针指向什么类型，值都是指针类型的大小，32位系统为4.具体sizeof的结果如下：　 

1、当操作数为数据类型时，所得结果为给数据类型的大小。如：32bit系统上，sizeof（char）＝ 1　，sizeof（int） ＝ 4。

2、当操作数是指针时，sizeof依赖于编译器。例如Microsoft　C/C++7.0中，near
类指针字节数为2，far、huge类指针字节数为4。一般Unix的指针字节数为4。　 

3、当操作数具有数组类型时，其结果是数组的总字节数。　 

4、联合类型操作数的sizeof是其最大字节成员的字节数。结构类型操作数的
sizeof是这种类型对象的总字节数，包括任何垫补在内。　 
    让我们看如下结构：　 
　　struct　
　　{
　　    char　b;
　　    double　x;
　　}　a;　 
　　在某些机器上sizeof（a）=12，而一般sizeof（char）+　sizeof（double）= 9。
　　这是因为编译器在考虑对齐问题时，在结构中插入空位以控制各成员对象的地址对
齐。如double类型的结构成员x要放在被4整除的地址。　 

5、如果操作数是函数中的数组形参或函数类型的形参，sizeof给出其指针的大小。
