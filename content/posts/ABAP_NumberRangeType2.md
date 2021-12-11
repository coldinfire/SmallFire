---
title: " 维护物料主数据的编号范围 "
date: 2019-07-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 简介

此项操作是在 SAP 系统后台，为需要使用的物料类型设定编号范围。它的操作是针对整个 Client 的。首先维护不重复的号码组，每组可包含内部段和外部段（两者可选其一或都含有），然后将需要设定的物料类型分派给号码组。

如果物料类型分派的号码组有内部号码段，那么在创建新物料主数据时不输入物料号，系统按自增式自动给出一个新号；如果有外部号码段，在创建新物料主数据时需输入物料号，系统检查是否符合编码规则。如果号码组含内、外号码段，则上述两种操作均可进行。

内部号码段只允许使用数字，外部则可以使用字母、下划线 (_)、连接线 (-)、数字等。

在维护新号码段之前，要查看是否和已存在的号码段冲突。若有，则可以通过以下两种方法解决：

- 修改原号码段维护的值；
- 重新设定新号码段的值。

### 进入维护界面

| IMG 路径                                                     | TCOCE | SNRO/SNUM维护值 |
| ------------------------------------------------------------ | ----- | --------------- |
| 后勤 - 常规→物料主数据→基本设置→物料类型→定义每个物料类型的号码范围 | MMNR  | MATERIALNR      |

### 查看及修改原有号码段

在创建新的号码组和段时，需要先查看目前的号码段是否有冲突，如有冲突需处理。

输入Tcode进入维护界面。

![MMNR](/images/ABAP/ABAP_NumberRange9.png)

选择修改Interval。

![Change interval](/images/ABAP/ABAP_NumberRange11.png)

### 维护

在前述准备工作完成后，就开始进行新的数据的操作了，在维护界面点击

"Change Group"，可以查看已有的物料类型维护到的号码组。

![Change Group](/images/ABAP/ABAP_NumberRange10.png)

#### 新建号码组

点击菜单"Group->Insert"创建组。

![Create Group](/images/ABAP/ABAP_NumberRange12.png)

维护号码段，并保存数据。

![Assign Number](/images/ABAP/ABAP_NumberRange13.png)

返回维护组界面,可见已有新号码组条目。

![Save Group](/images/ABAP/ABAP_NumberRange14.png)

#### 物料类型指定号码组

下一步需要将物料类型 “HAWA 贸易货物” 移至新建的号码组中。实际配置中，需分配的条目可能是在已存的号码组中，也许是在 “非分配元素” 组中，但重新分配的操作都是一样的。

勾选需要操作的组，选中 “HAWA 贸易货物” 条目，使其出现光标蓝色，点击分配元素组按钮。

![Assign Type](/images/ABAP/ABAP_NumberRange15.png)

操作完成。可见所选的物料类型条目已分配到新建的号码组。

![Assign Type](/images/ABAP/ABAP_NumberRange16.png)

点击确认按钮并退出。

#### 查看现有的编号段

在维护界面，再次按下修改间隔按钮，可以看到新的号码段已维护。

![Check](/images/ABAP/ABAP_NumberRange17.png)