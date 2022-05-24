---
title: " FB05 使用程序清账 "
date: 2022-03-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO

---

### FM：POSTING_INTERFACE_CLEARING

目的：财务分别做预收款和开 billing，需要后续做清账对冲 DZ 和 RV。

逻辑：由于只需要模拟人工 `F-32` 做清账，只需要根据 XBLNR 找到借贷方进行对冲即可，因此程序非常简单，核心传递的值为 it_ftclear-selfd。由于 VF01 开票中已经做了拆分增强，所以 Billing 的金额和 DZ 的金额是无法对应上的，因此无法采用标准自动清账配置，必须开发实现。

函数：`POSTING_INTERFACE_CLEARING`；POSTING_INTERFACE_CLEARING 并非纯函数，本质上是调用 BDC。

清账逻辑：取出RV、DZ、DG，将 XBLNR 字段相同的行组合在一起，清账。处理异常，事务代码：`F-32`。

- SE16 查询表 BSAD 按 BUKRS、AUGBL、GJAHR 查询，可查询到清账凭证行及原始行信息

```ABAP
FORM frm_create_ab.
*重点需要解决的两个问题：1. 清账函数的使用；
*                    2. 清账数据的准备（按照公司业务逻辑）.
  DATA: it_blntab  TYPE TABLE OF blntab WITH HEADER LINE,
        it_ftclear TYPE TABLE OF ftclear WITH HEADER LINE,
        it_ftpost  TYPE TABLE OF ftpost WITH HEADER LINE,
        it_fttax   TYPE TABLE OF fttax WITH HEADER LINE.
  DATA: lt_bsad_clearing TYPE TABLE OF bsad WITH HEADER LINE. "客户未清项表"
  DATA: lt_kunnr TYPE TABLE OF zthy_ecc_kunnr WITH HEADER LINE.
  DATA: lv_ftclear_agbuk TYPE bukrs. "清账公司代码"
  "每次处理一个客户的清账业务."
*宏.
*SELFD = Field Name from the Document Index
*SELVON = Input Field for Search Criterion for Selecting Open Items.
  DEFINE populate_ftclear.
    it_ftclear-agkoa  = &1.       "科目类型"
    it_ftclear-agbuk  = &2.       "公司代码"
    it_ftclear-xnops  = 'X'.      "标准未清项目"
    it_ftclear-AGUMS  = 'A'.      "特别总账未清项目"
    it_ftclear-selfd  = 'XBLNR'.  "凭证索引中的字段名(使用该字段搜索用来做对冲的借贷方)"
    it_ftclear-selvon = &3.       "Input Field for Search Criterion for Selecting Open Items"
    append it_ftclear.
  END-OF-DEFINITION.
 
  DEFINE populate_ftpost.
    it_ftpost-stype = &1.   "K为header，P为item"
    it_ftpost-count = &2.   "凭证抬头或行项目的计数器(记帐界面)"
    it_ftpost-fnam  = &3.   "BDC 字段名"
    it_ftpost-fval  = &4.   "BDC 字段值"
    append it_ftpost.
  END-OF-DEFINITION.
*1.Start of call transaction: SHDB.
  CALL FUNCTION 'POSTING_INTERFACE_START'
    EXPORTING
      i_client           = sy-mandt
      i_function         = 'C' "B= BDC, C= Call Trans"
      i_mode             = 'N' "N – no screen, A – all screen, E – Error"
      i_update           = 'S' "S: 数据更新完成后执行下一个操作"
      i_keep             = 'X' "用于已处理会话的队列删除标志"
      i_user             = sy-uname
    EXCEPTIONS
      client_incorrect   = 1
      function_invalid   = 2
      group_name_missing = 3
      mode_invalid       = 4
      update_invalid     = 5
      OTHERS             = 6.
  IF sy-subrc <> 0.
* Implement suitable error handling here
  ENDIF.
*2.Fill data for SHDB.
  populate_ftclear 'D' '1000' 'SA00012090'. "调用两次，一次借方，一次贷方."
*每执行一次添加一条清账行项目.如果借贷方分录不是正好等于两条，则应使用loop追加清账条目.
  populate_ftpost: 'K' 1 'BKPF-BUKRS' '1000',
                   'K' 1 'BKPF-BLART' 'AB',
                   'K' 1 'BKPF-BLDAT' sy-datum,
                   'K' 1 'BKPF-BUDAT' sy-datum,
                   'K' 1 'BKPF-XBLNR' 'SA00012090', "清账参考号"
                   'K' 1 'BKPF-WAERS' 'CNY'.
                   
**由于根据参考号(XBLNR)自动搜索清账的方式进行清账，因此行项目级别均不需要传值.
**               'P' 1 'RF05A-NEWBS' '17',
*                'P' 1 'RF05A-NEWBS' '18',  "通过FB05进行预收和应收对冲清账时，记账码为18（手工做F-32为17）
*                'P' 1 'RF05A-NEWUM' '',           "Special G/L indicator"
**               'P' 1 'BSEG-HKONT' '0011220001',
*                'P' 1 'BSEG-KUNNR' '0011000000',  "统驭科目"
**               'P' 1 'BSEG-WRBTR' '347.09',      "程序根据搜索条件自动搜出金额，无需指定"
*                'P' 1 'BSEG-ZFBDT' sy-datum,
*                'P' 1 'BSEG-SGTXT' '清账测试'.

  populate_ftpost: 'K' 2 'BKPF-BUKRS' '1000',
                   'K' 2 'BKPF-BLART' 'AB',
                   'K' 2 'BKPF-BLDAT' sy-datum,
                   'K' 2 'BKPF-BUDAT' sy-datum,
                   'K' 2 'BKPF-XBLNR' 'SA00012090', "清账参考号.
                   'K' 2 'BKPF-WAERS' 'CNY'.
**由于根据参考号(XBLNR)自动搜索清账的方式进行清账，因此行项目级别均不需要传值.
*                  'P' 2 'RF05A-NEWBS' '09',
*                  'P' 2 'RF05A-NEWUM' 'A',          "Special G/L indicator."
**                 'P' 2 'BSEG-HKONT' '0022030001',
*                  'P' 2 'BSEG-KUNNR' '0011000000',  "统驭科目"
**                 'P' 2 'BSEG-WRBTR' '347.09',      "程序根据搜索条件自动搜出金额，无需指定"
*                  'P' 2 'BSEG-ZFBDT' sy-datum,
*                  'P' 2 'BSEG-SGTXT' '清账测试'.
 
*3.Process BDC.
*每次只能传入一个清账分录.
*AUGLV  Purpose
*AUSGZAHL	Outgoing payment
*EINGZAHL	Incoming payment
*GUTSCHRI	Credit memo(W为承兑汇票，可能比较特殊)
*UMBUCHNG	Transfer posting with clearing
 
  CALL FUNCTION 'POSTING_INTERFACE_CLEARING' "Post with clearing (FB05) using internal posting interface
    EXPORTING
      i_auglv                    = 'AUSGZAHL'    " t041a-auglv   Clearing Transaction
      i_tcode                    = 'FB05'        " sy-tcode      Transaction code
      i_sgfunct                  = space         " rfipi-sgfunct  Different FUNCT function
    IMPORTING
      e_msgid                    = sy-msgid      " sy-msgid      Message ID 
      e_msgno                    = sy-msgno      " sy-msgno      Message number 
      e_msgty                    = sy-msgty      " sy-msgty      Message category 
      e_msgv1                    = sy-msgv1      " sy-msgv1      Message variable 1 
      e_msgv2                    = sy-msgv2      " sy-msgv2      Message variable 2 
      e_msgv3                    = sy-msgv3      " sy-msgv3      Message variable 3 
      e_msgv4                    = sy-msgv4      " sy-msgv4      Message variable 4
    TABLES
      t_blntab                   = it_blntab     " blntab        Table of the document numbers
      t_ftclear                  = it_ftclear    " ftclear       Clearing data
      t_ftpost                   = it_ftpost     " ftpost        Document header and item data
      t_fttax                    = it_fttax      " fttax         Taxes
    EXCEPTIONS
      clearing_procedure_invalid = 1
      clearing_procedure_missing = 2
      table_t041a_empty          = 3
      transaction_code_invalid   = 4
      amount_format_error        = 5
      too_many_line_items        = 6
      company_code_invalid       = 7
      screen_not_found           = 8
      no_authorization           = 9
      OTHERS                     = 10.
  IF sy-subrc EQ 0.
    "清账成功，清账凭证信息存储在it_blntab中.
  ELSE.
    "清账失败，查看importing中的消息.
  ENDIF.
*4.After bdc processing.
  CALL FUNCTION 'POSTING_INTERFACE_END'
    EXPORTING
      i_bdcimmed              = 'X'
    EXCEPTIONS
      session_not_processable = 1
      OTHERS                  = 2.
*5.log record.
ENDFORM.
```

