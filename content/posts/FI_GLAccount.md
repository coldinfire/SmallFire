---
title: " G/L Account 主要Tcode和tables "
date: 2020-07-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO

---

### 什么是G/L Account

首先，总账科目代表总账。

总帐会计的中心任务是提供外部会计和科目的全面情况。

在与公司所有其他业务领域完全集成的软件系统中记录所有业务交易（主要过帐以及内部会计结算）可确保会计数据始终完整且准确。

### SAP 总账科目主要功能

SAP FI 总帐具有以下功能：

- 自由选择级别：企业集团或公司
- 自动并同时过账适当总分类帐（对帐帐户）中的所有子分类帐项目
- 同时更新总帐和成本会计区域
- 以帐户显示，具有不同财务报表版本的财务报表和其他分析的形式，对当前会计数据进行实时评估和报告。

### 最重要的 SAP 总账科目代码

### SAP 总账科目主数据代码

| SAP GL Account Tcode | Description                                                | 中文描述                             |
| -------------------- | ---------------------------------------------------------- | ------------------------------------ |
| FSP0                 | Creation of G/L Account at Chart of Accounts Level         | 在科目表级别创建总账科目             |
| FSS0                 | Creation of G/L Account at Company Code Level              | 在公司代码级别创建总账科目           |
| FS00                 | Centrally Creation of G/L Account                          | 集中创建总账科目                     |
| FS10N                | Display G/L Account Balances                               | 显示总账科目余额                     |
| FS10NA               | Display G/L account balances (G/L Account Balance Display) | 显示总账科目余额（总账科目余额显示） |
| FBL3N                | Display G/L Account Balances for Open Item Managed A/cs    | 显示未清项目托管帐户的总账科目余额   |

#### SAP 中的总帐科目交易代码

用于管理过账，配置等的总账账户。

| Tcode   | Description                                                  | 中文描述                                                     |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| F-07    | Post Outgoing Payment for G/L Accounts                       | 总账科目过帐付款                                             |
| FB50    | G/L Account Posting – Enjoy Transaction                      | 总帐科目过帐–享受交易                                        |
| FS04    | To see the changes in the G/L Account Master                 | 查看总账科目主记录中的更改                                   |
| OBA0    | Define Tolerance Groups for G/L Accounts                     | 为总账科目定义公差组                                         |
| OBR2    | Deleting Master Data – Customers/ Vendors & G/L Accounts     | 删除主数据–客户 / 供应商和总账科目                           |
| OBY2    | Copy G/L Accounts from One Company Code to another           | 将总账科目从一个公司代码复制到另一个公司代码                 |
| OBXU    | Assign G/L Account for Automatic Posting of Discount Received | 分配总账科目以自动过账收到的折扣                             |
| OBXI    | Assign G/L Account for Automatic Posting of Discount Given   | 分配总账科目以自动过账折扣                                   |
| FI12    | Creation of House Bank and Assign G/L A/c in House Bank      | 创建房屋银行并分配房屋银行的总账 / 信用证                    |
| F-54    | Transfer of Advance from Special G/L to Normal by clearing Special G/L A/c | 通过清除特殊总账 / 提前付款 / 预付款将预付款从特殊总账转为正常 |
| F-39    | almost same as F-54                                          | 与 F-54 几乎相同                                             |
| F-32    | Clearing of Normal Item – Account Clear                      | 正常项目清算–帐户清算                                        |
| AO90    | Assignment of G/L Accounts for Automatic Postings            | 分配总账科目以自动过账                                       |
| F.16    | Carry Forward of G/L Account Balances                        | 结转总账科目余额                                             |
| FAGLL03 | Display G/L Account Line Items (G/L Account Line Items)      | 显示总帐科目行项目（总帐科目行项目）                         |

#### SAP 中的相关总账科目 Tcode

| TCode          | Description                                                  | 中文描述                             |
| -------------- | ------------------------------------------------------------ | ------------------------------------ |
| FB03           | Display invoice documents                                    | 显示发票文件                         |
| FBD3           | Display recurring documents                                  | 显示定期凭证                         |
| FBD4           | Display recurring entry document changes                     | 显示周期性的输入凭证更改             |
| S_ALR_87012277 | Display G/L account balances                                 | 显示总账科目余额                     |
| S_ALR_87012301 | Display G/L account balances                                 | 显示总账科目余额                     |
| S_ALR_87012287 | Display document journal of all postings by posting period   | 按过帐期间显示所有过帐的凭证日记帐   |
| S_ALR_87012293 | Display of Changed Documents                                 | 显示更改的文档                       |
| S_ALR_87012284 | Generate a financial statement (Balance Sheet/P+L Statement) | 生成财务报表（资产负债表 / 损益表）  |
| S_ALR_87012326 | Display chart of accounts (Chart of Accounts)                | 显示会计科目表（会计科目表）         |
| S_ALR_87012328 | Display list and printable list of G/L accounts master data  | 总账科目主数据的显示清单和可打印清单 |
| S_ALR_87012333 | Display list of G/L accounts (G/L accounts list)             | 显示总账科目列表（总账科目列表）     |
| S_ALR_87012291 | Displays overview of posting documents (Line Item Journal)   | 显示过帐凭证的概览（行项目日记帐）   |

### 重要的 SAP GL 帐户表

#### SAP GL 帐户主数据表

主数据的 SAP GL 帐户表是 SK * 表，总账科目主数据主要SAP表为：

| SAP GL Account tables | Description                                   | 中文描述                 |
| --------------------- | --------------------------------------------- | ------------------------ |
| SKA1                  | G/L Accounts (Chart of Accounts)              | 总账科目（科目表）       |
| SKAT                  | G/L Accounts (Chart of Accounts: Description) | 总账科目（科目表：描述） |
| SKB1                  | G/L Accounts (Company Code)                   | 总账科目（公司代码）     |



