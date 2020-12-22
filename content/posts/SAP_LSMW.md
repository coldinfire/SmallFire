---
title: "LSMW批处理使用方法"
date: 2020-09-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils
  - SAPbusiness

---

参考链接：[https://fenginfo.com/1821.html](https://fenginfo.com/1821.html)



## 总述

在 SAP 系统中，批处理操作有多种方法。如果是对一个事物码（TCODE）进行批处理操作，常用的是 LSMW。LSMW 全称是 Legacy System Migration Workbench。它能够对静态数据（如各类主数据）、动态数据（如初始化库存）、业务数据（如销售订单）等进行成批操作，是上线数据准备和运维期间大规模数据操作的主力武器。

LSMW 的原理是设定批处理的模板，再将准备好的格式化数据传到 SAP 系统进行预转换，如果合适就进行实际转换。

批处理模板可以有多种类型，包括系统已设定好的标准批输入对象（Standard Batch Input Object）、BAPI（Business Object Method）、IDC（Intermediate Document）、批输入记录（Batch Input Recorder）等；本文以批输入记录（Batch Input Recorder）为例进行介绍，此方法也可称为录像法。

LSMW 录像法批导入的原理是对需要批处理操作的流程进行操作录像，然后设定模板，再将准备好的数据传入到 SAP 系统进行预转换，如果合适就进行实际的转换。

LSMW 导入数据操作分为以下几大步骤：

- 准备需要导入的数据；
- 转换模板定义；
- 读取数据并预转换
- 实际转换

我们这个例子是在 SAP ERP 6.0 EHP7 下完成的，按经典的 14 个步骤进行讲解。

录像法只能对前台执行的事物码（T-CODE）进行录像，如果是后台配置，需转化成前台可执行 T-CODE、SM30 维护视图或是 SM34 维护视图簇方式进行操作，参见《[后台配置转至前台操作](https://fenginfo.com/89.html)》。

## 操作界面说明

### 管理界面

批导入的TCODE就是LSMW，在主窗口界面输入就可以进入。

![LSMW](/images/LSMW/LSMW1.png)

这个界面主要解决以下几方面问题：

（1）批导入对象的管理，包括新增、修改、删除、查找、导出、导入等操作。

（2）进入到其它工作界面，主要有录像操作界面、分步操作界面。

我们如果要进行一个批处理操作，则需要输入或通过选择确定 Project、Subproject、Object，如下图所示，然后再进行具体的操作。

![LSMW](/images/LSMW/LSMW2.png)

当批导出模板做好后，可以将其导出备份成本地文件，也可将本地的模板文件导入进 ERP 系统。通过导出、导入，可在不同 ERP 服务器之间互换模板资源。

![LSMW](/images/LSMW/LSMW3.png)

### 分步操作界面

在管理界面选择 Project、Subproject、Object，选择执行按钮进入分步操作界面。

![LSMW](/images/LSMW/LSMW4.png)

在分步操作的菜单中，用鼠标双击各菜单行可进入不同界面。这里共有 20 个步骤，在实际操作中我们不需要这么多，只需要 14 个就可以了，点击 “User Menu” 按钮进入选择用户菜单的对话框。在对话框中可以控制菜单显示。

![LSMW](/images/LSMW/LSMW5.png)

- Numbering Off：控制菜单数字序号的显示和隐藏
- Double Click = Display：控制双击菜单时是display还是直接change

如果执行了相应的步骤，界面的右侧会显示最后操作的日期、时间、操作者。

## 实际操作步骤

做一个完整的批处理操作，需要很多步骤。这些步骤分为几大部分：

- 数据准备(Step1)
- 创建批处理对象(Step2)
- 模板定义(Step3至Step9)，此部分又可以分为三个小步骤
  - 屏幕录像(Step3)
  - 数据源原表定义(Step4至Step6)
  - 转换字段对应(Step7至Step9)
- 预转换(Step10至Step15)，此部分又可以分为两个小步骤
  - 导入源表数据(Step10至Step13)
  - 预转换(Step14,15)
- 实际转换(Step16至Step18)

在以上操作中，除前三个步骤外，步骤 4 至步骤 18 均在分步操作界面下运行。

### [操作步骤详解](https://coldinfire.github.io/2020/SAP_LSMW_Details/)



