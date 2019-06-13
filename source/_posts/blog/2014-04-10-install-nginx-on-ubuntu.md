---
title: "nginx编译报错"
date: 2014-03-24
categories: 技术
tags: ["nginx"]
---
在ubuntu(32bit)上安装nginx执行configure时遇到错误如下：


    ./configure: error: the HTTP rewrite module requires the PCRE library.
    You can either disable the module by using --without-http_rewrite_module
    option, or install the PCRE library into the system, or build the PCRE library
    statically from the source with nginx by using --with-pcre=<path> option.
        
        
貌似缺少pcre库，在网上搜了，说是需要安装pcre-devel,可是ubuntu上安装是找不到pcre-devel。
可能是名字变了，后来终于找到解决办法，需要安装 libpcre3 libpcre3-dev：


    # sudo apt-get install libpcre3 libpcre3-dev











