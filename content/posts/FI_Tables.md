---
title: "SAP FI tables"
date: 2020-07-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO

---

### SAP FI 表：主数据

#### 总帐科目的 SAP 表

总账科目主记录包含总账确定科目功能所需的数据。

总帐科目主记录控制会计交易到总帐科目的过帐以及过帐数据的处理。

| G/L ACCOUNTS TABLES | DESCRIPTION                                  | IMPORTANT FIELDS              |
| ------------------- | -------------------------------------------- | ----------------------------- |
| SKA1                | G/L Accounts (Chart of Accounts)             | KTOPL /SAKNR                  |
| SKAT                | Description G/L Accounts (Chart of Accounts) |                               |
| SKB1                | G/L Accounts (Company Code)                  | BUKRS / SAKNR                 |
| SKM1                | Sample G/L accounts                          |                               |
| SKMT                | Sample G/L accounts Text                     |                               |
| SKAS                | G/L Account Master (Chart of Accounts)       | SPRAS / KTOPL / SAKNR / SCHLW |
| TSAKR               | Create G/L account with reference            | BUKRS / SAKNR                 |

#### SAP 客户主数据表

| CUSTOMER TABLES | DESCRIPTION                             | IMPORTANT FIELDS              |
| --------------- | --------------------------------------- | ----------------------------- |
| KNA1            | Customer master                         | KUNNR                         |
| KNB1            | Customer / company                      | KUNNR / BUKRS                 |
| KNVV            | Customer sales data                     |                               |
| KNBK            | Bank details                            | KUNNR / BANKS / BANKL / BANKN |
| KNVH            | Customer hierarchy(等级)                |                               |
| KNVP            | Customer partners                       |                               |
| KNVS            | Shipment data(出货数据) for customer    |                               |
| KNVK            | Contact persons                         |                               |
| KNVI            | Customer master tax indicator           |                               |
| KNC1            | Customer Master Transaction Figures     | KUNNR / BUKRS / GJHAR         |
| KNC3            | Customer Master Special GL Transactions | KUNNR / BUKRS / GJAHR / SHBKZ |

客户物料信息记录(Info Record)存储在表: **KNMT** – Customer Material Info Record

#### SAP 供应商主数据表

| VENDOR TABLES | DESCRIPTION                  | MAIN FIELDS   |
| ------------- | ---------------------------- | ------------- |
| LFA1          | Vendor master                | LIFNR         |
| LFB1          | Vendor per company code      | LIFNR / BUKRS |
| LFB5          | Vendor dunning data          |               |
| LFM1          | Purchasing organisation data |               |
| LFM2          | Purchasing data              |               |
| LFBK          | Bank details                 |               |

#### SAP银行主数据表

| BANK TABLES | DESCRIPTION                                                  |
| ----------- | ------------------------------------------------------------ |
| BNKA        | Master bank data                                             |
| T012-T      | House Banks T012A / B Allocation pmnt methods(分配方式) -> Bank transfer |
| T012C       | Terms for bank transactions(银行交易条款)                    |
| T012D       | Parameter for DMEs and foreign PM(DME 和外部 PM 的参数)      |
| T012E       | EDI-compatible house banks / PM                              |
| T023K       | House Bank Accounts                                          |
| T012O       | ORBIAN Detail: Bank Accounts                                 |
| TIBAN       | IBAN (International Bank Account Number)                     |

### SAPFI 会计核算凭证表

| ACCOUNTING DOCUMENT | DESCRIPTION                                        | 中文描述                         | MAIN FIELDS                   |
| ------------------- | -------------------------------------------------- | -------------------------------- | ----------------------------- |
| BKPF                | Accounting documents                               | 会计凭证                         | BUKRS / BELNR / GJAHR         |
| BSEG                | Item level                                         | 物品等级                         | BUKRS / BELNR / GJAHR / BUZEI |
| BSID                | Accounting: Secondary index for customers          | 会计：客户二级指标               |                               |
| BSIK                | Accounting: Secondary index for vendors            | 会计：供应商的二级索引           |                               |
| BSIM                | Secondary Index Documents for Material             | 物料的二级索引文件               |                               |
| BSIP                | Index for vendor validation of double documents    | 供应商验证双重文件的索引         |                               |
| BSIS                | Accounting: Secondary index for G/L accounts       | 会计：总帐科目的二级索引         |                               |
| BSAD                | Accounting: Index for customers (cleared items)    | 会计：客户索引（已清算项目）     |                               |
| BSAK                | Accounting: Index for vendors (cleared items)      | 会计：供应商索引（清算项目）     |                               |
| BSAS                | Accounting: Index for G/L accounts (cleared items) | 会计：总账科目索引（已清算项目） |                               |

### SAP 付款表

| SAP PAYMENT RUN TABLES | DESCRIPTION                                      | 中文描述                    |
| ---------------------- | ------------------------------------------------ | --------------------------- |
| REGUH                  | Settlement data from payment program             | 付款程序中的结算数据        |
| REGUP                  | Processed items from payment program             | 付款程序中的已处理项目      |
| REGUT                  | TemSe - Administration Data                      | TemSe 管理数据              |
| T042                   | Parameters for payment transactions              | 付款交易参数                |
| T042A                  | Bank selection for payment program               | 付款程序的银行选择          |
| T042B                  | Details on the company codes that pay            | 支付公司代码的详细信息      |
| T042C                  | Technical Settings: payment program              | 技术设置：付款程序          |
| T042D                  | Available amounts for payment program            | 付款程序可用的金额          |
| T042E                  | Company code-specific: payment methods           | 公司代码特定：付款方式      |
| T042F/H                | Payment method supplements                       | 付款方式补充                |
| T042G                  | Groups of company codes (payment program)        | 公司代码组（付款程序）      |
| T042I                  | Account determination for payment program        | 付款程序的科目确定          |
| T042J                  | Bank charges determination                       | 银行收费确定                |
| T042K                  | Accounts for bank charges                        | 银行收费帐户                |
| T042N                  | Bank transaction codes                           | 银行交易代码                |
| T042S                  | Charges/expenses for automatic pmnt transactions | 自动 pmnt 交易的费用 / 支出 |
| T042V                  | Value date for automatic payments                | 自动付款的起息日            |
| T042W                  | Permitted currency keys for payment method       | 付款方式允许的货币密钥      |
| T042Z                  | Payment method for automatic payment             | 自动付款的付款方式          |
| T008                   | Blocking reasons for automatic pmnt transactions | 自动 pmnt 交易的阻止原因    |

### SAP FI定制表

#### SAP公司代码表

| **COMPANY CODE TABLES** | DESCRIPTION                  | 中文描述         |
| ----------------------- | ---------------------------- | ---------------- |
| T004                    | Chart of accounts            | 会计科目表       |
| T007S                   | Account group (G/L accounts) | 帐户组(G/L 帐户) |
| T009                    | Fiscal year variants         | 会计年度变式     |
| T880                    | Global company data          | 全球公司数据     |
| T014                    | Credit control area          | 信用控制区       |

#### SAP FI 文档定制表

|       | DESCRIPTION                  | 中文描述         |
| ----- | ---------------------------- | ---------------- |
| T010O | Posting Period Variants      | 过帐期间变式     |
| T010P | Posting Period Variant Names | 过帐期间变体名称 |
| T001B | Permitted Posting Periods    | 允许的过帐期间   |
| T003  | Document Types               | 文件类型         |
| T012  | House Banks                  | 房屋银行         |

### SAP 税收表

| SAP TAX TABLES | DESCRIPTION                                                 |                                       |
| -------------- | ----------------------------------------------------------- | ------------------------------------- |
| T059A          | Type of Recipient for Vendors                               | 供应商的收件人类型                    |
| T059B          | Withholding Tax Classes for Vendors: Names                  | 供应商的预扣税类别：名称              |
| T059C          | Type of Recipient for Vendors per Withholding Tax Type      | 每个预扣税类型的供应商收件人类型      |
| T059D          | Type of Recipient for Vendors per Withholding Tax Type      | 每个预扣税类型的供应商收件人类型 Text |
| T059E          | Income Types T059F Formulas for Calc. Withholding Tax       | 收入类型 T059F 计算公式。预扣税       |
| T059G          | Income Types Names T059F Formulas for Calc. Withholding Tax | 收入类型名称 T059F 计算公式。预扣税   |
| T059K          | Withholding tax code; process. key                          | 预扣税代码                            |
| T059P          | Withholding tax types / tax code                            | 预扣税类型 / 税法                     |
| T059Z          | Withholding tax code (enhanced functions)                   | 预扣税法(增强功能)                    |
| T007A          | Tax Keys                                                    | 税金键                                |
| T007S          | Tax Keys Names                                              | 税金键名称                            |
| T007B          | Tax Processing in Accounting                                | 会计中的税务处理                      |

