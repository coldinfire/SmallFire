---
title: " FI 常用 BAPI "
date: 2020-03-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI
  - FICO

---

### FI BAPI List

| BAPI                           | Description                                                  |
| :----------------------------- | :----------------------------------------------------------- |
| K_HIERARCHY_TABLES_READ        | 成本要素组明细                                               |
| BAPI_ACC_DOCUMENT_POST         | 创建会计凭证                                                 |
| BAPI_ACC_DOCUMENT_REV_POST     | 反冲会计凭证；可以冲销自开发程序生成的凭证，必须传入交易码参数 |
| BAPI_ACC_GL_POSTING_REV_POST   | 只能冲销标准TCODE生成的凭证                                  |
| FCOM_COSTCENTER_CHANGEMULTIPLE |                                                              |
| FCOM_COSTCENTER_CREATEMULTIPLE |                                                              |
| BAPI_COSTCENTER_CHANGEMULTIPLE | 更改一个或多个成本中心                                       |
| BAPI_COSTCENTER_CHECKMULTIPLE  | 检查一个或多个成本中心                                       |
| BAPI_COSTCENTER_CREATEMULTIPLE | 创建一个或多个成本中心                                       |
| BAPI_COSTCENTER_DELETEMULTIPLE | 删除一个或多个成本中心                                       |
| BAPI_INCOMINGINVOICE_CREATE    | 发票检验：MIRO                                               |
| BAPI_INCOMINGINVOICE_CANCEL    | 发票校验冲销：mr8m                                           |

