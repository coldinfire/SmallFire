---
title: " 获取 Purchase Info Record Price "
date: 2021-03-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### Tables

- `EINA`：Purchasing Info Record: General Data
  - **INFNR**：Number of Purchasing Info Record
  - **MATNR**：Material Number
  - **LIFNR**：Vendor Account Number
- `EINE`：Purchasing Info Record: Purchasing Organization Data
  - **INFNR**：Number of Purchasing Info Record
  - **EKORG**：Purchasing Organization
  - **ESOKZ**：Purchasing info record category
  - **WERKS**：Plant
- `A017`：Material Info Record (Plant-Specific)
  - **LIFNR**：Vendor Account Number
  - **MATNR**：Material Number
  - **EKORG**：Purchasing Organization
  - **ESOKZ**：Purchasing info record category
  - **WERKS**：Plant
  - **KNUMH**：Condition record number
- `KONP`：Conditions (Item)
  - **KNUMH**：Condition record number
- `KONM`：Conditions (1-Dimensional Quantity Scale)
  - **KNUMH**：Condition record number, if the *KONP-KZBZG* eq 'C', there is a tiered price, normally, the highest price in *KONM* will be recorded into *KONP*.
- `EORD`：Purchasing Source List
  - **MATNR**：Material Number
  - **WERKS**：Plant
  - **LIFNR**：Vendor Account Number
  - **FLIFN**：Indicator: Fixed vendor

