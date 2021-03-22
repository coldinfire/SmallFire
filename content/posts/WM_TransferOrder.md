---
title: "Transfer Order(TO)"
date: 2020-05-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

#### Transfer Order

启用WM管理后，在BIN位间进行转移，通过Transfer Order 方式进行。

LT01:Create Transfer Order

LT03:Create Transfer Order for Delivery Note

LT06:Create Transfer Order for Material Document

LT1A:Change Transfer Order

LT11:Confirm Transfer Order Item

LT12:Confirm Transfer Order

LT15:Cancelling transfer order

LT21:Display Transfer Order

#### BAPI's List

- L_TO_CREATE_SINGLE : Create a transfer order with one item
- L_TO_CREATE_MULTIPLE : Create a transfer order with two or more items
- L_TO_CREATE_MOVE_SU : Create a transfer order to move a storage unit
- L_REF_CREATE : Create transfer orders using multiple processing
- L_TO_CREATE_2_STEP_PICKING : Create transfer orders for 2-step picking
- L_TO_CREATE_POSTING_CHANGE : Create transfer orders for posting changes
- L_TO_CREATE_TR : Create a transfer order for a transfer requirement
- L_TO_CONFIRM : Confirm transfer order

相关链接：

- [Cteater Transfer Order Single](https://coldinfire.github.io/2020/WMTO_1/)

