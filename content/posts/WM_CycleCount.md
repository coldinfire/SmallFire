---
title: " WM 盘点 "
date: 2020-12-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### 基本盘点流程

Step1：创建盘点凭证

- MI01：逐行创建盘点凭证
- MI31：批量输入创建盘点凭证（一次最多300行）

![Web Dynpro Create](/images/MM/CycleCount_1.png)

Step2：打印盘点凭证

- MI03：查看盘点凭证
- MI21：打印盘点凭证或 SE16 导出凭证明细

Step3：线下盘点

- 使用自开发的程序进行线下盘点的数据收集

Step4：录入实盘数

-  MI04：录入实盘数
-  将有差异的部分按照要求整理成 Excel；ABAP 开发程序调用 `BAPI_MATPHYSINV_COUNT` 导入Excel、执行程序录入实盘数
   - BAPI 传入实盘数时，行项目号要和盘点凭证一致，单位要传；如果盘点结果为 0，则应该设置 `ZeroCount = X` 

Step5：查询盘点差异

- MI20：查询盘点差异 

Step6：差异过账

- MI07：过账差异

#### SAP库存盘点相关的表

| Table | Description                         |
| :---- | :---------------------------------- |
| IKPF  | Header: Physical Inventory Document |
| ISEG  | Physical Inventory Document Items   |