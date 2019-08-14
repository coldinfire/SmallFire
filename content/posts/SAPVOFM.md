---
title: " VOFM使用 "
date: 2018-08-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils
 
---



SAP ERP 实施中，经常会用到例程开发 (TCODE:VOFM)。这个开发目前我用到的是影响 SD 和 MM 的定价过程。创建例程需要 ACCESS KEY，这个可以通过Basis申请得到，创建后例程会被包含在一个 REQUEST 下。写好代码以后，在 SPRO 里面的‘条件计算方案’将你写的代码编号配置进去，就可以影响到这个定价了。

​      ![](/images/ABAP/VOFM.png)

**例程工作原理**：

例程，即 Fomula，是使用在销售、采购、发票、交货等单据中定价过程的一小段程序。之所以有 Fomula 存在，是因为在不同的业务场景下，定价过程可能千差万别，但是却可以拆分为一些关键的组成部分，如复制请求、数据传输、要求、公式，每个例程就是一小段专用程序，这些例程程序会被标准程序动态调用：如：PERFORM XXX IN XXXX IF FOUND. 我们可以在例程中编写代码片段，修改运行环境中的数据。具体的每种例程都有不同的环境变量和接口数据，在此就不详细说明了。

创建例程的过程，实际上是做了以下几件事，我们**以routine 922 为例说明**：

- 创建了程序：RV61A922，该程序可通过 SE38 查看

- 在表 TFRM、TFRMT 中添加数据，记录创建的例程编号等信息

- 激活例程时，RV61A998 被 INCLUDE 在 RV61ANNN，即在 RV61ANNN 中添加一行：INCLUDE RV61A998.

**解释下传输后无法正常使用**

CHANGE REQUEST 释放后传输，1、2 两步可以正常完成，但是第三步，虽然在目标系统中激活了，但是未能 INCLUDE 在 RV61ANNN 程序中，因此定价过程配置好之后，会出现 ABAP DUMP.

**解决方法**：在目标系统中运行程序：RV80HGEN 即可修复 BUG，而不需要在目标系统中通过 VOFM 激活例程。该程序的作用是根据表 TFRM,TFRMT 在 RV61ANNN 等程序中增加 INCLUDE RV61A998 这样的代码，如此才能正常运行。具体见 Notes:28683

