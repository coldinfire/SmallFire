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

#### Step1：创建盘点凭证

- MI01：逐行创建盘点凭证
- MI31：批量输入创建盘点凭证（一次最多300行）

![Web Dynpro Create](/images/MM/CycleCount_1.png)

#### Step2：打印盘点凭证

- MI03：查看盘点凭证
- MI21：打印盘点凭证或 SE16 导出凭证明细

#### Step3：核查实际库存数量

- 使用自开发的程序进行线下盘点的数据收集

#### Step4：录入实盘数

-  MI04：录入实盘数
-  将有差异的部分按照要求整理成 Excel；ABAP 开发程序调用 `BAPI_MATPHYSINV_COUNT` 导入Excel、执行程序录入实盘数
   - BAPI 传入实盘数时，行项目号要和盘点凭证一致，单位要传；如果盘点结果为 0，则应该设置 `ZeroCount = X` 

#### Step5：查询盘点差异

- MI20：查询盘点差异，如果没有差异可以执行过账操作；如果有差异需要查找差异原因，再次核查实际的库存数量 
- MI05：更改盘点数据，将有差异并再次盘点的库存数量输入盘点凭证

#### Step6：差异过账

- MI07：盘点过账操作，如果有差异则生成物料凭证及财务凭证，无差异则只是关闭盘点凭证
- 如果 MI07 盘点完成发现错误，可选用两种方式修正数据
  - MI10：直接修正盘点数据
  - 重新盘点

### SAP库存盘点其它操作

#### 删除盘点凭证

创建盘点凭证后，该物料将被过账冻结，直至盘点完成后才能开解，在此期间不可产生物料凭证。如果想删除盘点凭证，可用TCODE：MI02 作删除标志。

#### 相关表

| Table | Description                         |
| :---- | :---------------------------------- |
| LQUA  | Material & Storage Bin Stock        |
| IKPF  | Header: Physical Inventory Document |
| ISEG  | Physical Inventory Document Items   |