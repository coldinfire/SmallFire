---
title: "工具合计"
date: 2018-08-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---


### 判断是否包含特定值

- IF field CN '0123456789'.
- IF field CN 'ABCDEFG*' 
- IF field CN 'abcdefg*'
- IF field CN '/' .....



| **CN：Contains Not Only (包含，不仅包含)** | **CO：Contains Only（仅包含）**           |
| ------------------------------------------ | :---------------------------------------- |
| **CS：Contains String (包含字符串)**       | **NS：Contains No String (不包含字符串)** |
| **NP：No Pattern （不包含记号）**          | **NA：Contains Not Only(不包含任何)**     |
| **CA：Contains Any（包含任何）**           | **CP：Covers Pattern (包含记号)**         |

### Commit 和 Rollback ###
```JS
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING
     wait = 'X'.
  IMPORTING
     return = commback.
CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'
  IMPORTING
     return = commback.
```
### 文本增强 ###

- T-CODE:CMOD->转到->文本增强->关键字->更改

### 查找Smartforms ###
- TCode：NACE可以查找
- Table：TNAPR根据smartform名字查找对应的smartform程序

### 批量删除后台作业 ###
- RSBTCDEL2 : 根据条件输入后台Job名或则根据用户天数，Commit尽可能大一点，Test Run勾上会显示满足条件的Job名字

### TCode ###
```JS
SPRO : 显示后勤
ST05 : Trace Sql 路径跟踪
ST22：ABAP Runtime Error
SE19 : BADI修改和创建
SE24 : 创建、修改、查询类的开发工具
SE21 : 创建、修改、查询包的开发工具
ST22 : 查看日志信息
SE91 : 消息管理
SE93 : 创建、修改、查询TCode的开发工具
SM12 : 编辑锁定解除
SM21 : 系统日志，记录系统的事件和问题
RZ01 : 图形化的工作监视 
SP01 : 打印需求查看
SE16N+&sap_edit:强行修改表数据
AL08 : 查看当前活动用户
AL11 : 应用服务器文件目录
VL06 : 交货监视器
VL08 : 拣配订单报表结果
VL11 : 销售订单，快速显示
SARA : 归档管理，批量删除数据
ME2L : 供应商采购凭证
MBST : 取消物料凭证
```