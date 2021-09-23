---
title: "WM 常用表"
date: 2020-05-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - WM

---

#### WM 常用表的功能划分

- Transfer requirement

- Transfer order

- Inventory Documents 

- Storage Locations

- Storage Stocks

#### SAP WM Tables for Transfer requirement

主要用于使用仓库管理系统（WMS）计划货物移动。

通过区分货物移动的计划和执行，您可以立即识别出是否仍然需要执行货物移动（未清转储需求），当前正在执行（已创建转储订单）还是已完成（已移转订单）。

| Transfer Requirement | Description                   |
| -------------------- | ----------------------------- |
| LTBK                 | Transfer requirement – header |
| LTBP                 | Transfer requirement – item   |

#### SAP WM Tables for Transfer order

在仓库管理（WM）的帮助下用于执行货物移动的文档。逻辑，实物货物移动或库存更改可以作为转储单的基础。

这些包括：
拣货(Picks) / 上架(Putaways) / 过帐更改(Posting changes) / 重新包装(Repacking) / 库存(Inventory)

| **Transfer Order** | Description             |
| ------------------ | ----------------------- |
| LTAK               | Transfer order - header |
| LTAP               | Transfer order - item   |
| LQUA               | Quants                  |

#### SAP WM Tables for Inventory document

SAP WM（仓库管理）中库存凭证的主要表

| **Inventory Doc.** | Description                        |
| ------------------ | ---------------------------------- |
| IKPF               | Physical Inventory Document Header |
| ISEG               | Physical Inventory Document Items  |
| LINK               | Inventory document header in WM    |
| LINP               | Inventory document item in WM      |
| LINV               | Data of inventory per quant        |

#### SAP WM Tables : Storage locations and stocks

SAP WM 中用于仓库管理中的存储位置和库存的重要表

| **Stock & Storage Loc** | Description                               |
| ----------------------- | ----------------------------------------- |
| LAGP                    | Storage bins                              |
| LEIN                    | Storage unit header records               |
| LQUA                    | Quants                                    |
| LQUAB                   | Total quant counts for certain strategies |
| MEIK                    | Make-to-Order Stock for Customer Order    |
| MSCA                    | Sales Orders on Hand with Vendor          |
| MSKA                    | Sales Order Stock                         |
| MBPR                    | Stock at Production Storage Bin           |
| MLGN                    | Material Data per Warehouse Number        |
| MLGT                    | Material Data per Storage Type            |

