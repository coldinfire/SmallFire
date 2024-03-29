---
title: " 维护客户号码范围 "
date: 2019-07-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 简介

定义并分配客户主数据的号码范围，分为两个步骤，两步操作的对象都针对整个 Client。

- 定义号码范围（Define Number Ranges for Customer Master）
- 分配给指定客户账户组（Assign Number Ranges to Account Groups）

### 定义号码范围

维护的号码段是由编号、号码起止段值组成，编号不可重复，段值不可重叠。它可以是内部编号也可以是外部，如果是内部，则只能由数字构成。

在维护新号码段之前，要查看是否和已存在的号码段值冲突。若有，则可以通过以下两种方法解决：

- 修改原号码段的段值
- 重新设定新号码段的段值

#### 操作步骤

| IMG路径                                                      | TCODE | SNRO/SNUM维护值 |
| ------------------------------------------------------------ | ----- | --------------- |
| 后勤 - 常规→业务合作伙伴→客户→控制→定义和分配客户号码范围 <定义客户主数据的编号范围> | OVZC  | DEBITOR         |

通过以上三种方法都可以进入操作界面：

![OVZC](/images/ABAP/ABAP_NumberRange6.png)

修改Intervals，新创建与旧数据无冲突的号码段并保存。

![Change interval](/images/ABAP/ABAP_NumberRange7.png)

### 分配号码范围

当号码范围定义完成后，还需要将其分配给指定的客户账户组，这样客户主数据的号码维护才算完整。

#### 操作步骤

| **IMG** **路径**                                             | **SM30** **维护视图** |
| ------------------------------------------------------------ | --------------------- |
| 后勤 - 常规→业务合作伙伴→客户→控制→定义和分配客户号码范围 <分配号码范围给科目组> | V_077D_B              |

通过以上两种方法进入操作界面：

![V_077D_B](/images/ABAP/ABAP_NumberRange8.png)

在号码范围栏输入相应的值，然后点击保存。

根据 Client 配置的不同（使用 TCODE：SCC4 维护）, 系统也许会弹出请求号输入对话框，新建或选定一个请求号继续执行。

