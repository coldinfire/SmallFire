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
### 字符串前拼接空格 ###
在Grid ALV上也显示敲到空格：
```JS
IF <fw_output>-name2+0(1) eq space AND <fw_output>-name2 IS NOT INITIAL.
  h_white = cl_abap_conv_in_ce=>uccpi( 160 ).
  REPLACE ALL OCCURRENCES OF REGEX '\s' IN <fw_output>-name2 WITH h_white.
ENDIF.
```
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
### MARM物料单位转换 ###
```JS
call function 'MD_CONVERT_MATERIAL_UNIT'
   exporting
     i_matnr                    = matnr
     i_in_me                    = in_me
     i_out_me                   = out_me
     i_menge                    = in_menge
   importing
     e_menge                    = out_menge
   exceptions
     error_in_application       = 1
     error                      = 2
     others                     = 3
               .
if sy-subrc <> 0.
   MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
 endif.
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