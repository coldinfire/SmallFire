---
title: " User Exit for MIGO "
date: 2020-08-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### MIGO 简介

内向交货的收货流程是供应链的重要组成部分。此过程包括创建采购订单后的步骤：通知，收货，随后的货物下达以及所订购货物的收货过帐。

根据未清采购订单数量处理好收货。使用称为移动类型的三位数密钥系统，将货物接收到工厂中并移动到其他存储位置。有助于区分货物移动。

好的收据通过以下方式记录货物或物料的向内移动：

- 根据订单验证收货
- 评估采购订单中的物料
- 创建物料和会计凭证以记录事件
- 为发票校验提供依据

### Goods receive 的结果

物料凭证的创建

- 成功过帐收货后，系统会自动创建一个物料凭证，以作为货物移动的凭证。

创建会计凭证

- 与物料凭证并行，系统还会创建会计凭证。会计凭证包含移动所需的过帐行（用于对应帐户）。

### Transaction Code – MIGO : Goods movement

| Exit Name | Description                                                |
| --------- | ---------------------------------------------------------- |
| MB_CF001  | Customer Function Exit in the Case of Updating a Mat. Doc. |
| MBCF0002  | Customer function exit: Segment text in material doc. item |
| MBCF0003  | Maintenance of batch master data for goods movements       |
| MBCF0004  | Maintenance of batch specifications for goods movements    |
| MBCF0005  | Material document item for goods receipt/issue slip        |
| MBCF0006  | Customer function for WBS element                          |
| MBCF0007  | Customer function exit: Updating a reservation             |
| MBCF0009  | Filling the storage location field                         |
| MBCF0010  | Customer exit: Create reservation BAPI_RESERVATION_CREATE1 |
| MBCF0011  | Read from RESB and RKPF for print list in MB26             |

