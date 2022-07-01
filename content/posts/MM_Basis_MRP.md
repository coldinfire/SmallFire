---
title: " MRP:物料需求计划 "
date: 2019-03-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### MRP ###

MRP：物料需求计划，利用生产日程表（MPS）、零件结构表（BOM）、库存报表、已订购未交货订单等等
各种相关资料，经正确计算而得出各种物料零件的变量需求，提出各种新的订购或修正各种以开出订购
的物料管理技术。

![MRP原理](/images/MM/MRP.png)
![MRP](/images/MM/MRP2.png)

#### MRP元素缩写

| Detail                 | Detail           | Detail           | Detail                     |
| ---------------------- | ---------------- | ---------------- | -------------------------- |
| SimReq：简单需求       | BR：流程订单     | PrdOrd：生产订单 | BtchSt：批量库存           |
| AR：相关预订           | OrdRes：订单需求 | PMOrdr：PM 订单  | CStock	客户库存         |
| BA：采购申请           | PurRqs：采购申请 | FE：生产订单     | DD：有效外日期             |
| BB：提供物料转包商需求 | SubReq：外协请求 | IH：维护订单     | E1：分包合同采购           |
| BE：订单项目计划行     | PrcOrd：处理订单 | CH：批库存       | FH：计划时界末             |
| BP：总需求计划         | OI-SL：采购订单  | KB：单独客户库存 | JI：JIT 提取/JIT：JIT 调用 |

