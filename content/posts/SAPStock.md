---
title: "SAP 库存查看"
date: 2020-05-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---



#### 显示物料的库存

MMBE:查看物料的所有库存信息。

| Stock Tcodes | Desc                                 | 中文描述                   |
| ------------ | ------------------------------------ | -------------------------- |
| MB51         | Material Doc. List                   | 物料凭证清单               |
| MB52         | List of Warehouse Stocks on Hand     | 现有仓库库存清单           |
| MB53         | Display Plant Stock Availability     | 显示工厂库存可用性         |
| MB54         | Consignment Stocks                   | 寄售库存                   |
| MB55         | Display Quantity String              | 显示数量字符串             |
| MB56         | Analyze batch where-used list        | 分析批次使用清单           |
| MB57         | Compile Batch Where-Used List        | 编译批处理使用清单         |
| MB58         | Consgmt and Ret. Packag. at Customer | 在客户处的                 |
| MB59         | Material Doc. List                   | 物料凭证清单               |
| MB5A         | Evaluate Batch Where-Used Archive    | 评估批次使用的存档         |
| MB5B         | Stocks for Posting Date              | 过帐日期的库存             |
| MB5C         | Pick-Up List                         | 拣配清单                   |
| MB5D         | Delete Docs of Batch Where-Used File | 删除批量使用文件的文档     |
| MB5E         | Create Batch Where-Used Archive      | 创建批处理在哪里使用的存档 |
| MB5K         | Stock Consistency Check              | 库存一致性检查             |
| MB5L         | List of Stock Values: Balances       | 股票价值清单：余额         |
| MB5M         | BBD/Prod. Date                       | BBD / 产品 日期            |
| MB5OA        | Display Valuated GR Blocked Stock    | 显示评估的收货冻结库存     |
| MB5S         | Display List of GR/IR Balances       | 显示 GR / IR 余额清单      |
| MB5T         | Stock in transit CC                  | 运送中 CC                  |
| MB5TD        | Stock in Transit on Key Date         | 重要日期在途库存           |
| MB5U         | Analyze Conversion Differences       | 分析转化差异               |
| MB5V         | Manage Batch Where-Used Archive      | 管理批量使用位置的存档     |
| MB5W         | List of stock values                 | 股票价值清单               |
| MMB1         | Create Semifinished Product          | 创建半成品                 |
| MMBE         | Stock Overview                       | 库存总览                   |

#### SAP WM 库存查看Tcodes

| WM Stock Tcodes | Desc                            | 中文描述               |
| --------------- | ------------------------------- | ---------------------- |
| LS24            | Display Quants for Material     | 显示物料的数量         |
| LS25            | Display Quants per Storage Bin  | 每个存储仓的显示数量   |
| LS26            | Warehouse stocks per material   | 每种物料的仓库库存     |
| LS27            | Display quants for storage unit | 显示存储单元的数量     |
| LS28            | Display storage units/bin       | 显示式储物单元 / Bin位 |
| LX02            | Stock List                      | 库存清单               |

#### SAP 库存类型

普通库存：正常的库存

- UR
- Quality
- Blocked 

特殊库存：特殊库存是由于其不属于您的公司或未存储在特定位置而被单独管理的库存

- Consignment：寄售
- Subcontracting：分包
- Stock transfer using stock transport order：使用库存运输订单进行库存转移
- Third-party processing：第三方处理
- Returnable transport packaging：可回收运输包装
- Pipeline handling：管道处理
- Sales order stock：销售订单库存
- Project stock：项目存量

#### SAP Stock Tables

| Table | Desc                                     | 中文描述                          | SOBKZ |
| ----- | ---------------------------------------- | --------------------------------- | ----- |
| MARC  | Plant Data for Material                  | 物料的工厂数据                    |       |
| MARD  | Storage Location Data for Material       | 物料的存储位置数据                |       |
| MCHB  | Batch Stocks                             | 批次库存                          |       |
| MKOL  | Special Stocks from Vendor               | 供应商的特殊库存                  | K/M   |
| MSKA  | Sales Order Stock                        | 销售订单库存                      | E     |
| MSKU  | Special Stocks with Customer             | 客户特殊库存                      | V/W   |
| MSLB  | Special Stocks with Vendor               | 与供应商的特殊库存                | O     |
| MSPR  | Project Stock                            | 项目存量                          | Q     |
| MSSA  | Total Customer Orders on Hand            | 现有客户订单总数                  | E     |
| MSSL  | Total Special Stocks with Vendor         | 与供应商的特殊库存总数            | O     |
| MSSQ  | Project Stock Total                      | 项目库存总计                      | Q     |
| MSTB  | Stock in Transit (as of 605)             | 在途库存（截至 605）              |       |
| MSTE  | Stock in Transit Sales Order (as of 605) | 在途销售订单中的库存（自 605 起） | E     |
| MSTQ  | Stock in Transit Project (as of 605)     | 在途中的库存（截至605 起）        | Q     |

#### SAP Stocks Valuation Tables

| Table | Desc                                               | 中文描述                             |
| ----- | -------------------------------------------------- | ------------------------------------ |
| MBEW  | Material Valuation                                 | 物料评估                             |
| EBEW  | Sales Order Stock Valuation                        | 销售订单库存评估                     |
| QBEW  | Project Stock Valuation                            | 项目库存评估                         |
| OBEW  | Valuated Stock with Subcontractor (only for Japan) | 带分包商的已估价库存（仅适用于日本） |

#### SAP Stocks Document Tables

| Table | Desc                                                | Key Fields            |
| ----- | --------------------------------------------------- | --------------------- |
| MKPF  | Header: Material Document                           | MBLNR & MJAHR         |
| MSEG  | Material Document – segments/ lines                 | MBLNR & MJAHR & ZEILE |
| BSIM  | Index for material to the FI-documents              | MATNR & BWKEY…        |
| CKMI1 | 物料分类帐索引，用于记录每个移动 KALNR 的 MBEW 变化 | MBEW-KALN1            |

#### SAP Stock Movement BAPI

- Create:  [BAPI_GOODSMVT_CREATE](https://sap4tech.net/sap-good-movement-bapi/)
- Cancel: [BAPI_GOODSMVT_CANCEL](https://sap4tech.net/sap-good-movement-bapi/)
- Get Details: **BAPI_GOODSMVT_GETDETAIL** / **BAPI_GOODSMVT_GETITEMS** 