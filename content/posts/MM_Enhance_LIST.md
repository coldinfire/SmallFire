---
title: " MM Enhance List "
date: 2019-08-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### BADIs for Purchasing

#### Commonly used PR BADIs

| BADI                 | Description                                            |
| -------------------- | ------------------------------------------------------ |
| ME_PROCESS_REQ_CUST  | PR BADI                                                |
| ME_REQ_POSTED        | Purchase Requisition Posted                            |
| ME_REQ_OI_EXT        | Commitment Update in the Case of External Requisitions |
| ME_COMMTMNT_REQ_RE_C | Check of Commitment Relevance of Purchase Requisitions |
| ME_COMMTMNT_REQ_RELE | Check of Commitment Relevance of Purchase Requisitions |

#### Commonly used PO BADIs

| BADI                                                         | Description                                                 |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [ME_PROCESS_PO_CUST](https://coldinfire.github.io/2019/MM_BADI_PurchaseOrder/) | PO BADI                                                     |
| ME_PROCESS_PO_CUST                                           | Enhancements for Processing Enjoy Purchase Order: Customer  |
| ME_PROCESS_PO                                                | Enhancements for Processing Enjoy Purchase Order: Intern.   |
| ME_PROCESS_COMP                                              | Processing of Component Default Data at Time of GR: Custome |
| ME_PO_SC_SRV                                                 | BADI：Service Tab Page for Subcontracting                   |
| ME_PO_PRICING_CUST                                           | Enhancements to Price Determination: Customer               |
| ME_PO_PRICING                                                | Enhancements to Price Determination: Internal               |
| ME_INFOREC_SEND                                              | Capture/Send Purchase Info Record Changes - Internal Use    |
| ME_HOLD_PO                                                   | Hold Enjoy Purchase Orders：Activation/Deactivation         |
| ME_GUI_PO_CUST                                               | Customer's Own Screens in Enjoy Purchase Order              |
| ME_FIELDSTATUS_STOCK                                         | FM Account Assignment Behavior for Stock PR/PO              |
| ME_COMMTMNT_PO_REL_C                                         | Check for Commitment-Relevance of Purchase Orders           |
| ME_COMMTMNT_PO_RELEV                                         | Check for Commitment-Relevance of Purchase Orders           |

### 物料凭证增强

#### BADI

 [MB_DOCUMENT_BADI](https://coldinfire.github.io/2019/MM_BADI_MaterialDocument/)

#### User Exit

MBCF0002 ：实现功能

- 当参照预留过帐时，检查填入数量是否小于预留数量
- 移动类型是 *** 的时候，查看 RSNUM 是否为空
- 检查原始单据工厂和库存地点与物料凭证的工厂和库存地点一致

MBCF009 ：实现功能

- 当移动类型是 ### 的时候，库存地点只能是 ###
- 工单下达日期 + 时间小于预留需求日期 + 时间，警告

#### Enhancement Spot

1、标准程序 MM07MFB0 

实现功能：如果移动类型是 ###，特殊库存标识必须是 *, 工厂必须是 ###

2、标准程序 MM07MFK0_KONTIERUNG_INIT

SPOT 是 ENHANCEMENT-POINT KONTIERUNG_INIT_01 SPOTS ES_SAPMM07M. 

实现功能：如果移动类型是 ###，特殊库存标识变成灰色，工厂变成灰色

3、标准程序 FM07MED0_DYNPRO_MODIFIZIEREN

SPOT 是 ENHANCEMENT-POINT DYNPRO_MODIFIZIEREN_06

SPOTS ES_FM07MED0_DYNPRO_MODIFIZIEREINCLUDE BOUND

实现功能：移动类型是 ###，则根据采购订单找到库存地点，讲库存地点描述替代到物料凭证的收货方 WEMPF 字段

4、标准程序 MM07MFF0_FUSSZEILE_WE

SPOT 是 ENHANCEMENT-POINT FUSSZEILE_WE_01 SPOTS ES_SAPMM07M. 

实现功能：如果移动类型是 ###，且特殊库存标志是 *，库存地点只能是 ###

### 物料主数据检查

#### BADI

BADI_MATERIAL_CHECK

### 预留增强

#### BADI

MB_RESERVATION_BADI

### 预制发票增强

#### BADI

INVOICE_UPDATE

- 实现功能：检查预制发票中采购订单对应的采购组、采购类型和采购组织的权限

### 供应商增强

#### BADI

VENDOR_ADD_DATA：创建供应商

VENDOR_ADD_DATA_CS ：供应商主数据屏幕增强