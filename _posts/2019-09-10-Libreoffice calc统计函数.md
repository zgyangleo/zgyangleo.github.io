---
layout: post
title:  "Libreoffice calc统计函数"
date:   2019-09-10 20:05:01 +0800
categories: jekyll
tag: jekyll
---

* content
{:toc}


[COUNTIF](#)

    在指定区域中按指定条件对单元格进行计数（单条件计数）

    countif函数的语法格式:

    =countif（range，criteria）

    参数range 表示条件区域——对单元格进行计数的区域。

    参数criteria 表示条件——条件的形式可以是数字、表达式或文本，甚至可以使用通配符。

    Countif（条件区域，条件）

![例]({{ '/styles/images/countif.png' | prepend: site.baseurl  }})

[VLOOKUP](#)

    VLOOKUP(根据什么找,到哪里找,找哪个,怎么找)

![例]({{ '/styles/images/vlookup.png' | prepend: site.baseurl  }})
 
    如图，根据C列查找，在$F$1:$G$174范围内查找，以$F$1:$G$174范围内的一一对应关系，在D列输出C列的匹配项。0指精确查找。


