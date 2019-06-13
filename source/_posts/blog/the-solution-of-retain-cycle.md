---
title: "如何解决retain cycle"
date: 2015-10-28
categories: 技术
tags: ["iOS"]
---

当遇到retain cycle是，一般我们的解决办法是让一个变为weak reference，而选择让那个变成weak reference是个问题。 我们可以把存在retain cycle的两个对象看成是 父－子 关系。 在这个关系当中，父可以拥有子，但是子不可以拥有父，因此，在这个问题中，我们可以把作为 父 的对象定义为strong， 而作为 子 的对象定义为weak，这样就解决了retain cycle。
