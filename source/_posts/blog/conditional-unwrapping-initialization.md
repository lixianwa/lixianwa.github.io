---
title: "Swift条件展开初始化"
date: 2016-09-06
categories: 技术
tags: ["Swift", "翻译"]
---

原文链接：[Idiomatic Swift: conditional unwrapping initialization](http://ericasadun.com/2016/08/04/idiomatic-swift-conditional-unwrapping-initialization/)

Swift-Users上，Dan T 问：   
目前我这样写：
```
let dobString: String
if let dob = dob {
  dobString = serverDateFormatter.stringFromDate(dob)
}
else {
  dobString = ""
}
```
有没有更好，更通用的方法实现同样的功能？

<!--more-->

我假设serverDateFormatter是NSDateFormatter的实例，这样的话，Dan这样写会更好：

```
let dobString: String = {
  guard let dob = dob else { return "" }
  return serverDateFormatter.string(from: dob)
}
```
我认为这样写更清晰，dobString只出现了一次。（如果他没有用NSDateFormatter，并且需要第二层展开，guard let语句需要第二个分句来执行条件绑定在stringFromDate返回的结果上）。

Update：Tim Vermeulen 有更漂亮的一行代码解决方案：
```
let dobString = dob.flatMap(serverDateFormatter.stringFromDate) ?? ""
```

我很确定以上代码中flatMap可以用map代替，这样可以节约四个字符。
你有更好的方案？可以在评论里留言，发推文，或者发到Swift-Users 邮件列表。