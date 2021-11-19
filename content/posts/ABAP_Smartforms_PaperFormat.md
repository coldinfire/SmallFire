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

![Smartforms](/images/ABAP/smartform_page4.png)

#### 定义页格式

事物码：`SPAD` 创建一个新的页格式(Page format)，页格式中指定了横打、竖打、宽、高等属性。

通过 copy 一个模板创建新的页格式 ZPAGE_NA：

![Page format](/images/ABAP/smartform_page5.png)

![Page format](/images/ABAP/smartform_page6.png)

![Page format](/images/ABAP/smartform_page7.png)

![Page format](/images/ABAP/smartform_page8.png)

#### 定义格式类型

格式类型(Format type)用于 ABAP LIST/SAPScript/Griphic 等。

创建新的格式类型 ZFORMAT_NA，设定属性中的页格式为步骤1中创建的 ZPAGE_NA。

![Format type](/images/ABAP/smartform_page9.png)

![Format type](/images/ABAP/smartform_page10.png)

![Format type](/images/ABAP/smartform_page11.png)

#### 分配格式给打印设备

SPAD 中查看打印设备名称类型。

![Devices servers](/images/ABAP/smartform_page12.png)

将创建的格式 ZFORMAT_NA 分配给指定设备类型。

![Devices types](/images/ABAP/smartform_page13.png)

![Devices types](/images/ABAP/smartform_page14.png)

![Devices types](/images/ABAP/smartform_page15.png)

选择之前创建的 ZFORMAT_NA。

![Devices types](/images/ABAP/smartform_page16.png)

![Devices types](/images/ABAP/smartform_page17.png)

创建完成后，可以在 smartform 中指定页格式 ZPAGE_NA。