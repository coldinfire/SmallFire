---
title: "Smartforms 纸张格式设置"
date: 2020-06-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 自定义打印纸张格式

当系统标准的纸张格式并不符合要求时，就需要自定义一种纸张格式了。

#### 定义页格式

事物码：SPAD 创建一个新的页格式(page format)，页格式中指定了横打/竖打， 宽 x 高等属性。

#### 定义格式类型

格式类型(format type)用于ABAP LIST/SAPScript/Griphic/。

创建新的格式类型 zfomat，设定属性中的页格式为步骤1中创建的 zpage。

#### 分配格式给打印设备

SPAD中查看打印设备名称类型，将创建的格式 zfomat 分配给指定设备类型。

创建完成后，可以在 smartform 中指定页格式 zpage。