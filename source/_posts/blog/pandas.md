---
title: "Pandas"
date: 2019-04-10T22:58:49+08:00
tags: ["Pandas", "数据分析"]
categories: 技术
draft: false
---

### Pandas 速查手册

对于数据科学家，无论是数据分析还是数据挖掘，如果用 Python作为工具，Pandas是一个非常重要的Python包，它不仅提供了很多方法，是的数据处理变得非常简单，同时由于底层使用Numpy，使得数据处理的速度也很快，比Python内置的方法有很大优势。

#### Pandas包导入
``` python
import pandas as pd
import numpy as np
```
<!--more-->

#### 数据导入

- pd.read\_csv(filename)  读取csv文件
- pd.read\_table(filename) 从限定分隔符的文本文件中导入数据
- pd.read\_excel(filename) 从excel文件中导入数据
- pd.read\_sql(query, connection\_object) 从sql表/库中导入数据
- pd.read\_json(json\_string) 从json格式的字符串中导入数据


未完待续……
