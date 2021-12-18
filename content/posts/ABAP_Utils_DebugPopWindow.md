---
title: " Debug 弹出框 "
date: 2021-08-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

参考链接：[https://mp.weixin.qq.com/s/R47RBR65JKUyyME1THMILg](https://mp.weixin.qq.com/s/R47RBR65JKUyyME1THMILg)

### Debug 弹出框示例

MM03 输入物料，会弹出框让你选视图：

![MM03](/images/ABAP/PopWindowDebug.png)

这个时候怎么deubg这个弹出框内的逻辑呢？

- 本地有个txt文本，拖拽到弹出框上。

  ![txt File](/images/ABAP/PopWindowDebug1.png)

  ![Debug](/images/ABAP/PopWindowDebug2.png)

- 然后继续操作，选择视图回车后进入弹出框逻辑。

  ![Debug](/images/ABAP/PopWindowDebug3.png)

#### Txt 内容

```tex
[FUNCTION]
Command=/H

Title=Debugger
Type=SystemCommand
```

