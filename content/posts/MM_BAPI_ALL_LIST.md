---
title: " MM BAPI List "
date: 2020-07-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI

---

###  MM 常用 BAPI 整理

| BAPI                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [BAPI_MATERIAL_SAVEDATA](http://localhost:1313/2020/MM_BAPI_MaterialSaveData/) | 创建及更改物料主数据，EXTENSIONIN可以创建自定义字段          |
| BAPI_OBJCL_CREATE                                            | 分类视图的创建                                               |
| BAPI_OBJCL_GETCLASSES                                        | 分类视图得到详细信息                                         |
| BAPI_MATERIAL_SAVEREPLICA                                    | 物料视图的扩充                                               |
| [BAPI_GOODSMVT_CREATE](https://coldinfire.github.io/2019/MM_BAPI_GoodMovementCreate/) | 创建物料凭证，注意表T158G可以决定 goodsmvt_code              |
| [BAPI_GOODSMVT_CANCEL](https://coldinfire.github.io/2019/MM_BAPI_GoodsMovementCancel/) | 冲销物料凭证                                                 |
| [BAPI_PR_CREATE](https://coldinfire.github.io/2020/MM_BAPI_PurchaseRequestCreate/) | 创建 Purchase Requisition New                                |
| [BAPI_REQUISITION_CREATE](https://coldinfire.github.io/2020/MM_BAPI_PurchaseRequestCreate/) | 创建 Purchase Requisition Old                                |
| [BAPI_PO_CREATE1](https://coldinfire.github.io/2020/MM_BAPI_PurchaseOrderCreate/) | 创建 Purchase Order                                          |
| [BAPI_PO_CHANGE](https://coldinfire.github.io/2020/MM_BAPI_PurchaseOrderCreate/) | 修改 PO 和删除 PO                                            |
| WS_REVERSE_GOODS_ISSUE                                       | 冲销交货单的过账发货                                         |
| BAPI_RESERVATION_CREATE1                                     | 创建预留，如果要检查 ATP必须使用 BAPI_RESERVATION_CREATE     |
| BAPI_RESERVATION_CHANGE                                      | 修改和删除预留                                               |
| BAPI_BILLINGDOC_CREATEMULTIPLE                               | 创建发票                                                     |
| [BAPI_ACC_DOCUMENT_POST](https://coldinfire.github.io/2020/FI_ACC_DOCUMENT_POST/) | 创建会计凭证                                                 |
| BAPI_MATERIAL_AVAILABILITY                                   | 可用库存                                                     |
| BAPI_SALESORDER_CREATEFROMDAT2                               | 创建销售订单                                                 |
| BAPI_OUTB_DELIVERY_CREATE_SLS                                | 根据销售订单创建交货单                                       |
| PRICES_CHANGE & PRICES_POST                                  | 更改物料移动平均价或者标准价格，如果要检查 ATP 必须使用第二个 |