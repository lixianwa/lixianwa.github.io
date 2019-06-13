---
title: "markdown语法"
date: 2015-10-12
categories: 教程
tags: ["markdown"]
---
本文转自[这里](http://blog.izhangbo.cn/掌握这几种markdown语法你就够了/)
 
使用 Markdown 真的可以写出非常整洁的文档来，看到很多人都在使用，自己也忍不住学习了下。网上有很多讲解 markdown 语法的，资料庞大复杂不够简练，学个基础语法置于这么麻烦么。我做了一些整理，保证你学了这些，就可以使用 markdown 去秀文档了。

```
1、标题：

#h1级标题
##h2级标题
###h3级标题
####h4级标题
#####h5级标题
######h6级标题

2、分割线：三个以上的短线 即可作出分割线(需要空行隔开生效)

3、超链接：[连接名称](网址 , 标题)
[我是链接名](http://www.izhangbo.cn, “我是标题”)
[<i class=”icon-refresh”></i> 点我刷新](/sonfilename/)

另一种超链接写法：[链接名][链接代号]
[here][3]
然后在别的地方定义 3 这个详细链接信息，[3]: http://www.izhangbo.cn “haode”

4、键盘键
<kbd>Ctrl+[</kbd> and <kbd>Ctrl+]</kbd>

5、code格式：反引号
Use the `printf()` function.
“There is a literal backtick (`) here.针对在代码区段内插入反引号的情况“

6、强调：
*斜体强调*
**粗体强调**

7、图片
![Alt text](/path/to/img.jpg “Optional title”)

8、上角标
这里加个上角标[^stackedit]

[^stackedit]:角标内容详情

9、使用 icon 图标文字
<i class=”icon-cog”></i>

10、段落：以一个空行开始，以一个空行结束，中间的就是一个段落。

11、表格：这个稍微有些复杂
Item | Value
——– | —
Computer | $1600
Phone | $12
Pipe | $1

12、无序列表：使用 – 加一个空格(需要空行隔开生效)

– 无需列表1
– 无序列表2
– 无序列表3

13、有序列表：使用 数字 加一个英文句点(需要空行隔开生效)
1. 有序列表
2. 有序列表
3. 有序列表
4. 有序列表
5. 有序列表

14、行缩进：使用 “:加空格”
: 这里有个空格，可进行行缩进。

15、换行缩进形成代码区块

这里先换行，然后缩进4个空格，之后的内容便可以原样显示了，适合用于显示代码内容。直到文本结束或最后一个存在缩进的行为止。
16、块引用
>给引用的文本开始位置都加一个 ‘>’，
>便可组成一个块引用。在块引用中，可以结合
>其他markdown元素一块使用，比如列表。
>**强调**
也可以只在第一行加大于号，其他位置不加。

>- 块引用里使用列表，需要和上面的内容隔开一个空行
>- 记得加空格哦。

如果你要在 markdown 中使用特殊字符，需要对它们进行转义。
```
如果你对 markdown 的来龙去脉很感兴趣，并参与研究，请移步[这里](https://github.com/riku/Markdown-Syntax-CN/blob/master/syntax.md)。
