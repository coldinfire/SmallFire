---
title: " BTE 增强使用 "
date: 2019-12-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### BTE概念

BTE(**Business Transaction Events**)：BTE是SAP中可用的增强技术之一，通常使用在财务会计模块,可由 SAP，第三方供应商（合作伙伴）和客户使用。SAP程序通过调用`OPEN_FI_PERFORM_<Number>`或`OUTBOUNT_CALL_<Number>`函数来调用BTE。

以下是两种可用于实现BTE的类型：P/S 为检查用的 BTE，Processes 是修改用的。

![BTE Type](/images/ABAP/BTE5.png)

这些接口通知外部软件某些事件已在 SAP 标准应用程序中发生，并向其提供产生的数据。外部软件不会向 SAP Standard System 返回任何数据。它们不会以任何方式影响标准 R / 3 程序。

![BTE事件](/images/ABAP/BTE6.png)

![BTE提供的FM Template](/images/ABAP/BTE7.png)

![FM Tempate](/images/ABAP/BTE8.png)

### BTE查找技巧

调用 BTE 的相关函数

- BF_FUNCTIONS_FIND
- PC_FUNCTION_FIND

1）在 FM：`BF_FUNCTIONS_FIND`，设断点，然后查看 `CALL FUNCTION 'BF_FUNCTIONS_READ'` 里面的变量，可以
  查看这个流程，会触发哪些 event，以及哪些customer FM，这样就可以在相应的 EVENT 开发对应的 ZFM。

2）查找对应合适的 BTE：运行事务码 `XD02`，查找到对应的程序为 `SAPMF02D`，在此程序中搜索字符串
  `OPEN_FI_PERFORM`，可以找到此程序中的所有用到的 BTE。  

### 配置BTE

Tcode：FIBF

![BTE](/images/ABAP/BTE1.png)

![Add entries](/images/ABAP/BTE3.png)

![创建P/S BTE](/images/ABAP/BTE2.png)

根据系统提供的事件模板，创建自定义的FM，并实现相应的业务逻辑。

![选择对应的事件和处理的函数](/images/ABAP/BTE4.png)

###  BTE程序创建

将光标放在已识别的 BTE 进程号上，单击示例功能模块，然后将其复制到 ZFM* 中并编写自己的功能。

![FM Tempate](/images/ABAP/BTE9.png)

![FM Tempate Implement](/images/ABAP/BTE10.png)

### BTE系统间传输

通过FIBF创建和配置完成的内容需要传输到其他系统，需要选中对应的Item，并执行下图步骤。可以将配置内容包含在TR中进行传输。

![Transfer](/images/ABAP/BTE11.png)

###   BTE 相关的Tcode 和 Table

SAP Reference IMG -> Financial Accounting -> Financial Accounting Global Settings -> Business Transaction Events

#### BTE事件与相关触发Tcode

- F-02
- VF01
- FB70
- FB01

#### BTE存储的表

TBE01 ：Library of the Publish&Subscribe Business Transaction Events

TBE01T ：P&S BTE: Language-Specific Descriptions

#### BTE相关的TCode

| TCode | Description                   | TCode | Description                     |
| ----- | ----------------------------- | ----- | ------------------------------- |
| FIBF  | Maintenance transaction BTE   | BF34  | Customer Modules per Event      |
| BERE  | Business Event Repository     | BF41  | Application Modules per Process |
| BERP  | Business Processes            | BF42  | Partner Modules per Process     |
| BF31  | Application modules per Event | BF44  | Customer Modules per Process    |
| BF32  | Partner Modules per Event     |       |                                 |


