---
title: "ME2N/ME2M报表增加显示字段"
date: 2020-04-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

原文地址：[http://blog.csdn.net/zhongguomao/article/details/52514197](http://blog.csdn.net/zhongguomao/article/details/52514197)



### 在 ME2N/ME2M/ME2W/ME3M 里加 EKKO/EKPO 中没显示的字段

#### EKKO/EKPO中存在该字段

在结构 MEREP_OUTTAB_PURCHDOC 里 APPEND 字段就可以了。

- SE11 中显示结构：MEREP_OUTTAB_PURCHDOC，然后 APPEND 所需要的字段即可；
- 然后去 ME2N/ME2M 或 ME3M 里执行之后点击上面的更改格式按钮就可看到它的值。

#### EKKO/EKPO中不存在的字段

需要通过增强实现。

- 进程序 LMEREPI02，找到方法 build_base_list, 在里面建立 enhancement implementation
- 在增强里面写代码给字段赋值
- 首先都要在 MEREP_OUTTAB_PURCHDOC APPEND 字段

#### 选择界面上增加查询字段

1、做一个自由点增强，比如在  INCLUDE FM06LCS1中，最后行增加你的选择参数？INCLUDE FM06LTO1中加也要添加。其它地方也可加.

2、选择的处理代码

- 如是 EKKO 中的条件则可写在  PERFORM selpa_check_kopf (sapfm06l) 中，

- 如是 EKPO 中的条件则可写在  PERFORM selpa_check_pos (sapfm06l). 
- 其它很多地方也可以写处理代码，仔细找一下应该有更合适的地方都可以写。