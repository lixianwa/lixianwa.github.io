---
title: "一个iOS View crash问题"
date: 2015-10-15
categories: 技术
tags: ["iOS"]
---
[2015-10-15]	
用xib做了一个简单的界面，程序启动时会crash掉，xcode error打印信息：	
>Terminating app due to uncaught exception 'UIViewControllerHierarchyInconsistency', reason: 'A view can only be associated with at most one view controller at a time! View <UIView: 0x7f914af22610; frame = (0 0; 414 736); autoresize = W+H; layer = <CALayer: 0x7f914af22980>> is associated with <UIViewController: 0x7f914ae49a90>. Clear this association before associating this view with <TestViewController: 0x7f914ac7f680>.'
