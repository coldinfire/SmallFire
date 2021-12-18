---
title: " BAPI 销售订单修改 "
date: 2019-10-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### VA02：销售订单修改

UPDATEFLAGS：flg 值的三种不同意义

- U = change 
- D = delete
- I = add

```ABAP
*&---------------------------------------------------------------------*
*&      Form  FRM_CHANGE_SALESORDER
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->P_LT_ITEM  text
*      -->P_LT_COND  text
*      -->P_LT_MSG  text
*      -->P_LS_HEAD  text
*      <--P_LV_EIND  text
*----------------------------------------------------------------------*
FORM frm_change_salesorder  TABLES   tp_item    STRUCTURE zrmxsds004
                                     tp_cond    STRUCTURE zrmxsds005
                                     tp_message STRUCTURE zifsret01
                            USING    up_head LIKE zrmxsds003
                            CHANGING cp_eind TYPE c. 
  DATA: ls_item LIKE zrmxsds004,
        ls_cond LIKE zrmxsds005,
        ls_msg  LIKE zifsret01,
        ls_vbep LIKE vbep.
  DATA: wa_header  TYPE bapisdh1,    "表头"
        wa_headerx TYPE bapisdh1x,   "表头标志"
        wa_partner  TYPE bapiparnr,  "业务伙伴"
        wa_partnerc TYPE bapiparnrc,
        wa_item    TYPE bapisditm,   "行项目"
        wa_itemx   TYPE bapisditmx,
        wa_cond    TYPE bapicond,    "价格条件"
        wa_condx   TYPE bapicondx,
        wa_schdl   TYPE bapischdl,   "交付计划"
        wa_schdlx  TYPE bapischdlx,
        wa_return  TYPE bapiret2,
        wa_sdls    TYPE bapisdls,
        wa_text    TYPE bapisdtext.  "文本"
  DATA: lt_partner  TYPE STANDARD TABLE OF bapiparnr,
        lt_partnerc TYPE STANDARD TABLE OF bapiparnrc,
        lt_item    TYPE STANDARD TABLE OF bapisditm,
        lt_itemx   TYPE STANDARD TABLE OF bapisditmx,
        lt_schdl    TYPE STANDARD TABLE OF bapischdl,
        lt_schdlx   TYPE STANDARD TABLE OF bapischdlx,
        lt_cond    TYPE STANDARD TABLE OF bapicond,
        lt_condx   TYPE STANDARD TABLE OF bapicondx,
        lt_return  TYPE STANDARD TABLE OF bapiret2,
        lt_text    LIKE STANDARD TABLE OF bapisdtext.
  DATA: lt_sokey TYPE STANDARD TABLE OF zrmxsds015,
        ls_sokey TYPE zrmxsds015.
* Header
  CLEAR: wa_header,wa_headerx.
  IF up_head-updateflag   = cns_update.
    wa_header-pmnttrms    = up_head-zterm.  "付款条件"
    wa_headerx-pmnttrms   = cns_yes.
    wa_headerx-updateflag = cns_update.     "Update"
    " Header texts：表头文本,若传输空值，则清空该字段 "
    CLEAR: wa_text,lt_text[].
    wa_text-itm_number    = space.
    wa_text-text_id       = cns_textid.
    wa_text-langu         = sy-langu.
    wa_text-format_col    = '*'.
    wa_text-text_line     = up_head-tknum.  "运输合同号"
    APPEND wa_text TO lt_text.
  ENDIF.
* Partners
  IF up_head-kunnr_re IS NOT INITIAL.  "收票方"
    CLEAR: wa_partnerc.
    wa_partnerc-document = up_head-vbeln.
    wa_partnerc-itm_number = '000000'.
    wa_partnerc-updateflag = cns_update.
    wa_partnerc-partn_role = 'RE'.
    wa_partnerc-p_numb_new = up_head-kunnr_re.
    APPEND wa_partnerc TO lt_partnerc.
  ENDIF.
  IF up_head-kunnr_rg IS NOT INITIAL.  "付款方"
    CLEAR: wa_partnerc.
    wa_partnerc-document = up_head-vbeln.
    wa_partnerc-itm_number = '000000'.
    wa_partnerc-updateflag = cns_update.
    wa_partnerc-partn_role = 'RG'.
    wa_partnerc-p_numb_new = up_head-kunnr_rg.
    APPEND wa_partnerc TO lt_partnerc.
  ENDIF.
  IF up_head-kunnr_we IS NOT INITIAL.  "送达方"
    CLEAR: wa_partnerc.
    wa_partnerc-document = up_head-vbeln.
    wa_partnerc-itm_number = '000000'.
    wa_partnerc-updateflag = cns_update.
    wa_partnerc-partn_role = 'WE'.
    wa_partnerc-p_numb_new = up_head-kunnr_we.
    APPEND wa_partnerc TO lt_partnerc.
  ENDIF.
* Items
  REFRESH: lt_item,  lt_cond, lt_schdl,
           lt_itemx, lt_condx,lt_schdl.
  LOOP AT tp_item INTO ls_item.
    IF ls_item-updateflag = cns_new.    "新增行项目"
      CLEAR wa_item.
      wa_item-itm_number = ls_item-posnr.
      wa_item-material   = ls_item-mabnr.   "物料"
      wa_item-sales_unit = ls_item-vrkme.   "计量单位"
      wa_item-plant      = ls_item-werks.   "工厂"
      wa_item-store_loc  = ls_item-lgort.   "库存地"
      APPEND wa_item TO lt_item.
      "行状态"
      wa_itemx-itm_number = ls_item-posnr.
      wa_itemx-updateflag = cns_new.
      wa_itemx-material   = cns_yes.
      wa_itemx-sales_unit = cns_yes.
      wa_itemx-plant      = cns_yes.
      wa_itemx-store_loc  = cns_yes.
      APPEND wa_itemx TO lt_itemx.
      "Schedule lines"
      CLEAR: wa_schdl,wa_schdlx.
      wa_schdl-itm_number = ls_item-posnr.
      wa_schdl-req_qty    = ls_item-kwmeng.  "数量"
      APPEND wa_schdl TO lt_schdl.
      wa_schdlx-itm_number = ls_item-posnr.
      wa_schdlx-updateflag = cns_new.
      wa_schdlx-req_qty    = cns_yes.
      APPEND wa_schdlx TO lt_schdlx.
      "新增行，需要对自动生成的生产订单进行下达"
      CLEAR ls_sokey.
      ls_sokey-vbeln = up_head-vbeln.
      ls_sokey-posnr = ls_item-posnr.
      APPEND ls_sokey TO lt_sokey.
    ELSEIF ls_item-updateflag = cns_update.  "更新行项目信息"
      "Schedule line 仅行数量"
      CLEAR: wa_schdl,wa_schdlx.
      wa_schdl-itm_number = ls_item-posnr.
      wa_schdl-sched_line = '0001'.          "默认都是第一行"
      wa_schdl-req_qty    = ls_item-kwmeng.  "数量"
      APPEND wa_schdl TO lt_schdl.
      wa_schdlx-itm_number = ls_item-posnr.
      wa_schdlx-sched_line = '0001'.
      wa_schdlx-updateflag = cns_update.
      wa_schdlx-req_qty    = cns_yes.
      APPEND wa_schdlx TO lt_schdlx.
    ENDIF.
  ENDLOOP.
* Item Conditions
  wa_sdls-cond_handl = cns_yes.   "价格条件，需要设置该参数，才能够修改价格条件"
  LOOP AT tp_cond INTO ls_cond.
    IF ls_cond-updateflag = cns_new.    "新增价格条件记录"
      CLEAR: wa_cond,wa_condx.
      wa_cond-itm_number = ls_cond-posnr.
      wa_cond-cond_type  = ls_cond-kschl.  "定价条件"
      wa_cond-cond_value = ls_cond-kbetr.  "价格"
      wa_cond-currency   = ls_cond-koein.  "货币或%"
      wa_cond-cond_unit  = ls_cond-kmein.  "条件单位"
      wa_cond-cond_p_unt = ls_cond-kpein.  "条件定价单位"
      APPEND wa_cond TO lt_cond.
      wa_condx-itm_number = ls_cond-posnr.
      wa_condx-cond_type  = ls_cond-kschl.  "定价条件"
      wa_condx-updateflag = cns_new.
      wa_condx-cond_value = cns_yes.  "价格"
      wa_condx-currency   = cns_yes.  "货币或%"
      wa_condx-cond_unit  = cns_yes.  "条件单位"
      wa_condx-cond_p_unt = cns_yes.  "条件定价单位"
      APPEND wa_condx TO lt_condx.
    ELSEIF ls_cond-updateflag = cns_update.   "更新价格条件记录"
      CLEAR: wa_cond,wa_condx.
      "需要读取已经存在行的Key"
      PERFORM frm_get_cond_key USING up_head-vbeln
                                     ls_cond-posnr
                                     ls_cond-kschl
                            CHANGING wa_cond-cond_st_no
                                     wa_cond-cond_count.
      wa_cond-itm_number = ls_cond-posnr.
*      wa_cond-cond_st_no = 040.
*      wa_cond-cond_count = 01.
      wa_cond-cond_type  = ls_cond-kschl.  "定价条件"
      wa_cond-cond_value = ls_cond-kbetr.  "价格"
      wa_cond-currency   = ls_cond-koein.  "货币或%"
      wa_cond-cond_unit  = ls_cond-kmein.  "条件单位"
      wa_cond-cond_p_unt = ls_cond-kpein.  "条件定价单位"
      APPEND wa_cond TO lt_cond.
      wa_condx-itm_number = ls_cond-posnr.
      wa_condx-cond_st_no = wa_cond-cond_st_no.
      wa_condx-cond_count = wa_cond-cond_count.
      wa_condx-cond_type  = ls_cond-kschl.
      wa_condx-updateflag = cns_update.
      wa_condx-cond_value = cns_yes.
      wa_condx-currency   = cns_yes.
      wa_condx-cond_unit  = cns_yes.
      wa_condx-cond_p_unt = cns_yes.
      APPEND wa_condx TO lt_condx.
    ELSE.
      "报错"
    ENDIF.
  ENDLOOP.
* Call BAPI
  CALL FUNCTION 'BAPI_SALESORDER_CHANGE'
    EXPORTING
      salesdocument               = up_head-vbeln
      order_header_in             = wa_header
      order_header_inx            = wa_headerx
*     SIMULATION                  =
*     BEHAVE_WHEN_ERROR           = ' '
*     INT_NUMBER_ASSIGNMENT       = ' '
      logic_switch                = wa_sdls
*     NO_STATUS_BUF_INIT          = ' '
    TABLES
      return                      = lt_return
      order_item_in               = lt_item
      order_item_inx              = lt_itemx
*      partners                    = lt_partner
      partnerchanges              = lt_partnerc
*     PARTNERADDRESSES            =
*     ORDER_CFGS_REF              =
*     ORDER_CFGS_INST             =
*     ORDER_CFGS_PART_OF          =
*     ORDER_CFGS_VALUE            =
*     ORDER_CFGS_BLOB             =
*     ORDER_CFGS_VK               =
*     ORDER_CFGS_REFINST          =
      schedule_lines              = lt_schdl
      schedule_linesx             = lt_schdlx
      order_text                  = lt_text
*     ORDER_KEYS                  =
      conditions_in               = lt_cond
      conditions_inx              = lt_condx
*   EXTENSIONIN                 =
       .
* 处理错误消息:通过判断消息的类型，来判断BAPI是否成功
  LOOP AT lt_return INTO wa_return.
    CLEAR ls_msg.
    ls_msg-class   = 'BUS'.
    ls_msg-msgtyp  = wa_return-type.
    ls_msg-msgno   = wa_return-number.
    ls_msg-msgtxt  = wa_return-message.
    APPEND ls_msg TO tp_message.
    IF wa_return-type EQ 'E' OR wa_return-type = 'A' OR wa_return = 'X'.
      cp_eind = 'X'.  "失败"
    ENDIF.
  ENDLOOP.
  IF cp_eind NE 'X'.
    CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
      EXPORTING
        wait = 'X'.
  ELSE.
    CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK' .
  ENDIF.
  CHECK cp_eind NE 'X' AND lt_sokey[] IS NOT INITIAL.
* 销售订单自动产生生产订单，对生产订单进行下达
  CALL FUNCTION 'Z_RMXPP_PRDORD_RELEASE'
    EXPORTING
      I_WAIT                           = 3
    TABLES
      t_sokey                          = lt_sokey
     EXCEPTIONS
       no_saleorders                    = 1
       no_valid_saleorders              = 2
       cannot_find_product_orders       = 3
       OTHERS                           = 4.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
       WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
ENDFORM.                    " frm_create_salesorder "
*&---------------------------------------------------------------------*
*&      Form  FRM_GET_COND_KEY
*&---------------------------------------------------------------------*
*       读取价格条件记录的Key
*----------------------------------------------------------------------*
*      -->P_UP_HEAD_VBELN  text
*      -->P_LS_COND_POSNR  text
*      -->P_LS_COND_KSCHL  text
*      <--P_WA_COND_COND_ST_NO  text
*      <--P_WA_COND_COND_COUNT  text
*----------------------------------------------------------------------*
FORM frm_get_cond_key  USING    up_vbeln LIKE vbap-vbeln
                                up_posnr LIKE vbap-posnr
                                up_kschl LIKE konv-kschl
                       CHANGING cp_st_no LIKE konv-stunr
                                cp_count LIKE konv-zaehk.
  DATA: lv_knumv LIKE vbak-knumv.S
  " 由于需要多次判断，预先读取聚集表,将订单的所有行读取出来 "
  IF gt_konv[] IS INITIAL.
    SELECT SINGLE knumv INTO lv_knumv
      FROM vbak
     WHERE vbeln = up_vbeln.
    SELECT knumv kposn kschl stunr zaehk
      INTO CORRESPONDING FIELDS OF TABLE gt_konv
      FROM konv
     WHERE knumv = lv_knumv.
*        AND kposn = up_posnr.
    SORT gt_konv BY kposn kschl.
  ENDIF.
  CLEAR gwa_konv.
  READ TABLE gt_konv INTO gwa_konv WITH KEY kposn = up_posnr
                                            kschl = up_kschl
                                            BINARY SEARCH.
  IF sy-subrc EQ 0.
    cp_st_no = gwa_konv-stunr.
    cp_count = gwa_konv-zaehk.
  ENDIF.
ENDFORM.                    " FRM_GET_COND_KEY "
```

