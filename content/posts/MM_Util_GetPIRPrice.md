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
- `KONV`：Conditions for Transaction Data
- `KONP`：Conditions (Item)
  - **KNUMH**：Condition record number
- `KOND`：Conditions(Data)
- `KONH`：Conditions(Header)
- `KONM`：Conditions (1-Dimensional Quantity Scale)
  - **KNUMH**：Condition record number, if the *KONP-KZBZG* eq 'C', there is a tiered price, normally, the highest price in *KONM* will be recorded into *KONP*.
- `EORD`：Purchasing Source List
  - **MATNR**：Material Number
  - **WERKS**：Plant
  - **LIFNR**：Vendor Account Number
  - **FLIFN**：Indicator: Fixed vendor
- `LFA1`：Vendor Info
  - **LIFNR：Vendor Account Numbe**r

### Condition Types T685T Table

标准表 T685T 包含定价的所有 Condition Types。

T685T Condition Types 表的字段是： 

| **T685T FIELDS** | DESCRIPTION                  |
| :--------------- | :--------------------------- |
| SPRAS            | Language Key                 |
| KVEWE            | Usage of the condition table |
| KAPPL            | Application                  |
| KSCHL            | Condition Type               |
| VTEXT            | Name                         |

#### SAP Most Common Condition Types

| **CONDITION TYPES** | DESCRIPTION          |
| :------------------ | :------------------- |
| A001                | Rebates              |
| FRA1                | Freight %            |
| HB00                | Header Surch.(Value) |
| HB01                | Header Disc.(Value)  |
| K000                | Contrct HeaderDisc % |
| MM00                | Minimum Qty (Amount) |
| MM01                | Minimum Qty (%)      |
| MP01                | Market Price         |
| MWST                | Input Tax            |
| P000                | Gross Price          |
| R000                | Discount % on Gross  |
| PA00                | Promotion Price      |
| P001                | Gross Price          |
| PRS                 | Total Price          |