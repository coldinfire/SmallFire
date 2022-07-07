---
title: " 透明表删除Key值后激活 "
date: 2020-04-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

当业务场景发生变化，或则其它原因需要变更透明表的 Key 值时，保存和激活时会报错，需要使用 SE14 进行处理。

![SE14](/images/ABAP/ABAP_SE14_1.png)

勾选Direct,Save Data;然后点击Activate and adjust database。

![执行](/images/ABAP/ABAP_SE14_2.png)

返回透明表，保存，激活即可。