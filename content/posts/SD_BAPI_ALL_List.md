---
title: " SD 模块常用BAPI "
date: 2019-10-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### SD 常用 BAPI

| BAPI                           | Description                                                  |
| :----------------------------- | :----------------------------------------------------------- |
| SD_SALESDOCUMENT_CREATE        | 创建销售订单                                                 |
| SD_SALES_DOCUMENT_SAVE         | 从复制的文档创建销售订单                                     |
| BAPI_SALESORDER_CREATEFROMDAT2 | 创建销售订单                                                 |
| BAPI_SALESORDER_CHANGE         | 修改或者删除销售订单                                         |
| BAPI_OUTB_DELIVERY_CREATE_SLS  | 根据销售订单创建交货单                                       |
| BAPI_BILLINGDOC_CREATEMULTIPLE | 创建发票，注意参数 ref_doc_ca                                |
| MB_CANCEL_GOODS_MOVEMENT       | 冲销交货单的过账发货                                         |
| BAPI_BILLINGDOC_CANCEL1        | 发票的冲销                                                   |
| BAPI_INB_DELIVERY_SAVEREPLICA  | 创建 Inbound Delivery                                        |
| GN_DELIVERY_CREATE             | 单个创建 Outbound Delivery                                   |
| SHP_VL10_DELIVERY_CREATE       | 创建 Outbound Delivery                                       |
| BAPI_OUTB_DELIVERY_CHANGE      | 修改外向交货单                                               |
| SD_DELIVERY_UPDATE_PICKING     | 修改外向交货单拣配数量                                       |
| WS_DELIVERY_UPDATE             | 外向交货单的发货过账                                         |
| BAPI_OUTB_DELIVERY_CONFIRM_DEC | 用于来自分散系统的外向交货验证的 BAPI ，可以使用此方法将外向交货从 WM 系统报告回 (ERP) 系统。 |
| SD_CUSTOMER_MAINTAIN_ALL       | 创建客户；table 参数中有很多表，其中X开头代表要插入的数据，Y开头代表要删除的数据。 |
| SD_SALES_DOCUMENT_READ         | 读取销售单据标题和业务数据：tables VBAK, VBKD and VBPA (Sold-to (AG), Payer (RG) and Ship-to (WE) parties) |
| SD_SALES_DOCUMENT_READ_POS     | 读取销售凭证抬头和物料：表 VBAK、VBAP-MATNR                  |
| SD_DOCUMENT_PARTNER_READ       | 合作伙伴信息，包括地址。 调用 SD_PARTNER_READ                |
| SD_PARTNER_READ                | 所有合作伙伴信息和地址                                       |
| SD_DETERMINE_CONTRACT_TYPE     | 确定文档是服务合同还是数量合同                               |
| SD_SALES_DOCUMENT_COPY         | 将销售单据复制到具有所需销售单据类型 (VBAK-AUART) 的新单据中，以便进一步创建。 Example：O - 创建后续文档 |
| SD_SALES_DOCUMENT_ENQUEUE      | 出列使用 DEQUEUE_EVVBAKE                                     |
| SD_PACKING_PRINT_VIEW          | 用于 Dlv 便笺打印的 SD 数据收集                              |
| SD_DELIVERY_VIEW               | 从 RV_DELIVERY_PRINT_VIEW、SD_PACKING_PRINT_VIEW 调用的打印数据收集 |
| SD_COND_ACCESS                 | 选择条件记录                                                 |
| RV_DELIVERY_CREATE             | 创建 Delivery                                                |
| RV_BILLING_PRINT_VIEW          | 用于开票凭证打印的数据提供                                   |
| RV_DELIVERY_PRINT_VIEW         | 为交货单打印提供数据                                         |
| RV_PRICE_PRINT_HEAD            | 用于打印程序以获取标题 [和行项目] 级别的定价数据。 输入：结构 KOMK（字段 mandt、kalsm、waerk、knumv、vbtyp 取自 VBDKR，kappl=‘V’）。 [和 KOMP（字段 kposn 取自 VBDPR，字段 mglme（数量）可以更改以相应地计算价格]。输出：表 TKOMV [和 TKOMVD] 中的定价数据。 |
| RV_ORDER_FLOW_INFORMATION      | 在交货和开票后读取销售凭证的销售凭证流，不要忘记检查您正在搜索的起始凭证 # 和前/后凭证类型的直接参考凭证（例如，如果您搜索给定 SO 的交货， 如果在性能方面可能，还请检查 LIPS-VGBEL 和 LIPS-VGPOS）。 |

