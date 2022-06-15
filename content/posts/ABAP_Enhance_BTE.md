---
title: " BTE 增强使用 "
date: 2019-12-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---

### BTE概念

BTE(**Business Transaction Events**)：BTE 是 SAP 中可用的增强技术之一，通常使用在财务会计模块，可由 SAP、第三方供应商（合作伙伴）和客户使用。SAP 程序通过调用`OPEN_FI_PERFORM_<Number>`或`OUTBOUNT_CALL_<Number>`函数来调用 BTE。**BTE 增强实施的核心工作是寻找合适的事件号码**。

BTE 的设计思路更加简单，和 BADI 有点类似。在标准程序中留有 OPEN_FI 的出口，提供一个可配置的 Table，可以在里面针对某个特定的 Event 维护自己定义的出口函数，标准程序走到这里，如果查出用户定义了出口函数则会调用，达到了增强的目的。

SAP 提供的这些接口通知外部软件某些事件已在 SAP 标准应用程序中发生，并向其提供产生的数据。外部软件不会向 SAP Standard System 返回任何数据。它们不会以任何方式影响标准 R / 3 程序。

以下是两种可用于实现 BTE 的类型：类似于会计凭证的验证和替代

![BTE Type](/images/ABAP/BTE5.png)

- P/S 函数模块 （Publish and Subscribe Interface）：只提供 SAP 数据源，可以供外部程序使用或则达到数据检查的目的
- Processes 函数模块（Process Interface）：可以达到数据修改的目的，用来增强标准的业务流程

选择 Info system (P/S)：

![BTE事件查询](/images/ABAP/BTE12.png)

执行后可以看到许多常用的事件号及事件描述，选择其中一个选择 **Sample function module** 即可跳转到对应的 FM：

![BTE事件](/images/ABAP/BTE6.png)

![BTE提供的FM Template](/images/ABAP/BTE7.png)

![FM Tempate](/images/ABAP/BTE8.png)

### BTE 查找

如果需要的 EVENT 在标准提供列表中不存在，这时就需要手动去寻找 EVENT Number。

#### 查找方法

调用 BTE 的相关函数

- BF_FUNCTIONS_FIND
- PC_FUNCTION_FIND

1）在 FM：`BF_FUNCTIONS_FIND`，设断点；进入断点后查看 `CALL FUNCTION 'BF_FUNCTIONS_READ'` 里面的变量 **I_EVENT** 中的事件号，通过表 **TBE01** 可以查看事件的描述及对应的函数模块；这样就可以在相应的 EVENT 开发对应的 ZFM*。

![FM BF_FUNCTIONS_FIND](/images/ABAP/BTE13.png)

2）查找对应合适的 BTE：在标准程序中查找字符串 `OPEN_FI_PERFORM`，如果找到的函数结尾是 `_E`，则说明是 **P/S 函数模块**；如果是`_P`，则是 **Processes 模块**。  

![FM BF_FUNCTIONS_FIND](/images/ABAP/BTE14.png)

### 配置 BTE

####  创建 Function Module 

找到 EVENT 后复制其对应的函数模块，将其复制到 ZFM* 中并编写自己的功能。

![FM Tempate](/images/ABAP/BTE9.png)

![FM Tempate Implement](/images/ABAP/BTE10.png)

#### 创建产品：FIBF

![BTE](/images/ABAP/BTE1.png)

![Add entries](/images/ABAP/BTE3.png)

#### 配置产品，事件及函数模块

![创建P/S BTE](/images/ABAP/BTE2.png)

![选择对应的事件和处理的函数](/images/ABAP/BTE4.png)

配置完成后，在自定义的 FM 中打断点，看是否能进入 Debug 模式。如果进入 Debug 模式，则配置 BTE 增强成功。

### BTE 系统间传输

通过 FIBF 创建和配置完成的内容需要传输到其他系统，需要选中对应的 Item，并执行下图步骤。可以将配置内容包含在 TR 中进行传输。

![Transfer](/images/ABAP/BTE11.png)

###   BTE 相关的 Tcode 和 Table

SAP Reference IMG -> Financial Accounting -> Financial Accounting Global Settings -> Business Transaction Events

#### BTE 存储的表

TBE01：Library of the Publish&Subscribe Business Transaction Events

TBE01T：P&S BTE Language-Specific Descriptions

#### BTE 相关的 TCode

| TCode | Description                   | TCode | Description                     |
| ----- | ----------------------------- | ----- | ------------------------------- |
| FIBF  | Maintenance transaction BTE   | BF34  | Customer Modules per Event      |
| BERE  | Business Event Repository     | BF41  | Application Modules per Process |
| BERP  | Business Processes            | BF42  | Partner Modules per Process     |
| BF31  | Application modules per Event | BF44  | Customer Modules per Process    |
| BF32  | Partner Modules per Event     |       |                                 |


