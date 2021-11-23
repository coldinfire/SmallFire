---
title: " 批量创建/修改物料主数据 "
date: 2020-08-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### BAPI_MATERIAL_SAVEDATA 使用实例

```ABAP
REPORT ZUPDATE_MATNR.
"$. Region BAPI Data"
DATA gs_bapimathead TYPE bapimathead. " 物料号 物料类型 物料视图表 "
DATA gs_bapi_mara   TYPE bapi_mara.   " MARA数据-基本视图数据 "
DATA gs_bapi_marax  TYPE bapi_marax.  " 变更数据标识 "
DATA gs_bapi_mvke   TYPE bapi_mvke.   " 销售视图 "
DATA gs_bapi_mvkex  TYPE bapi_mvkex.
DATA gs_bapi_marc   TYPE bapi_marc.   " 工厂级别数据 "
DATA gs_bapi_marcx  TYPE bapi_marcx.
DATA gs_bapi_mpgd   TYPE bapi_mpgd.   " 计划数据 "
DATA gs_bapi_mpgdx  TYPE bapi_mpgdx.
DATA gs_bapi_mbew   TYPE bapi_mbew.   " 会计成本视图 "
DATA gs_bapi_mbewx  TYPE bapi_mbewx.
DATA gs_bapi_mard   TYPE bapi_mard.   " 存储位置 "
DATA gs_bapi_mardx  TYPE bapi_mardx. 
DATA gs_bapi_mlgn   TYPE bapi_mlgn.   " 仓库数据 "
DATA gs_bapi_mlgnx  TYPE bapi_mlgnx.
DATA gs_bapi_mlgt   TYPE bapi_mlgt.   " 存储类型数据 "
DATA gs_bapi_mlgtx  TYPE bapi_mlgtx.
DATA gs_return      TYPE bapiret2.    " 返回参数 "

DATA gt_bapi_makt   LIKE TABLE OF bapi_makt WITH HEADER LINE.   " 物料描述 "
DATA gt_bapi_marm   LIKE TABLE OF bapi_marm WITH HEADER LINE.   " 物料的单位换算 "
DATA gt_bapi_marmx  LIKE TABLE OF bapi_marmx WITH HEADER LINE.
DATA gt_bapi_mlan   LIKE TABLE OF bapi_mlan WITH HEADER LINE .  " 税分类 "
DATA gt_bapi_mltx   LIKE TABLE OF bapi_mltx WITH HEADER LINE .  " 文本数据 "
DATA gt_return1 LIKE TABLE OF  bapi_matreturn2 WITH HEADER LINE." 返回信息 "

DATA:gs_bapi_te_mara  LIKE bapi_te_mara,     " 自定i增强字段:客户级别的物料数据 "
     gs_bapi_te_marax LIKE bapi_te_marax.
DATA:gs_bapi_te_e1mvke  LIKE bapi_te_e1mvke, " 自定义增强字段：销售视图 "
     gs_bapi_te_e1mvkex LIKE bapi_te_e1mvkex.
DATA:gt_extensionin  LIKE TABLE OF  bapiparex WITH HEADER LINE, " 附加结构 "
     gt_extensioninx LIKE TABLE OF  bapiparexx WITH HEADER LINE.
DATA:l_valuepart(960),l_valuepartx(960).

DATA: pr_unit TYPE meins,  " 基本单位 "
      pr_unit2 TYPE meins. " 重量单位 "
"$. Endregion BAPI Data"

"$. Region Structure"
DATA:BEGIN OF itab OCCURS 0,
  head_material TYPE matnr,      " 物料号 "
  head_ind_sector TYPE mbrsh,    " 行业领域 "
  head_matl_type TYPE mtart,     " 物料类型 "
  t_makt_matl_desc TYPE maktx,   " 物料描述 "
  mara_base_uom TYPE meins,      " 基本计量单位 "
  mara_matl_group TYPE matkl,    " 物料组 "
  t_old_mat_no TYPE bismt,       " 型号 "
  mara_division TYPE spart,      " 产品组 "
  t_dsn_office TYPE labor,       " 实验室 / 办公室 "
  mara_item_cat TYPE mtpos_mara, " 普通项目组类别 "
  t_marm_gross_wt TYPE brgew,    " 毛重 "
  mara_unit_of_wt TYPE gewei,    " 重量单位 "
  mara_net_weight TYPE ntgew,    " 净重 "
  mara_size_dim TYPE groes,      " 大小 / 量纲 "
  zz_model TYPE char30,          " 屏幕增强字段 "
  zz_drawing TYPE char27,        " 屏幕增强字段 "
  zz_color TYPE char20,          " 屏幕增强字段 "
END OF itab.
DATA itab1 TYPE itab OCCURS 0 WITH HEADER LINE.
* 原材料
DATA: BEGIN OF str_material,
  marc_plant TYPE werks_d,   "工厂"
  mard_stge_loc TYPE lgort_d,"库存地点
  mvke_sales_org TYPE vkorg, "销售组织
  mvke_distr_chan TYPE vtweg,"分销渠道
  head_material TYPE matnr,  "物料号
  head_ind_sector TYPE mbrsh,"行业领域
  head_matl_type TYPE mtart, "物料类型
  t_makt_matl_desc TYPE maktx,"物料描述
  mara_base_uom TYPE meins,  "基本计量单位
  mara_matl_group TYPE matkl,"物料组
  mara_extmatlgrp TYPE extwg,"外部物料组
  mara_item_cat TYPE mtpos_mara,"普通项目组类别
  t_marm_gross_wt TYPE brgew,"毛重
  t_marm_unit_of_wt TYPE gewei,"重量单位
  mara_net_weight TYPE ntgew,"净重
  t_marm_volume TYPE volum,  "标准箱
   t_marm_volumeunit TYPE voleh,"体积单位
  mvke_sales_unit TYPE vrkme,"销售单位 

  t_mlan_taxclass1 TYPE taxkm,  "税分类1
  t_mlan_taxclass2 TYPE taxkm,  "税分类2
  mvke_matl_stats TYPE stgma,   "物料统计组
  mvke_acct_assgt TYPE ktgrm,   "科目设置组
  mvke_item_cat TYPE mtpos,     "来自物料主文件的项目主类别
  marc_availcheck TYPE mtvfp,   "可用性检查
  mara_trans_grp TYPE tragr,    "运输组
  marc_loadinggrp TYPE ladgr,   "装载组
  marc_pur_group TYPE ekgrp,    "采购组
  marc_batch_mgmt TYPE xchpf,   "批次管理标示
  marc_auto_p_ord TYPE kautb,   "自动采购订单
  marc_ind_post_to_insp_stock TYPE insmk_mat,"过账到检验库存
  marc_quotausage TYPE usequ,   "配额安排
  marc_sourcelist TYPE kordb,   "源清单
  marc_mrp_group TYPE disgr,    "MRP组
  marc_mrp_type TYPE dismm,     "MRP类型
  marc_mrp_ctrler TYPE dispo,   "MRP控制者
  marc_lotsizekey TYPE disls,   "批量
  marc_minlotsize TYPE bstmi,   "最小批量
  marc_maxlotsize TYPE bstma,   "最大批量
  marc_round_val TYPE bstrf,    "舍入值
  marc_proc_type TYPE beskz,    "采购类型
  marc_backflush TYPE rgekm,    "反冲
  marc_plnd_delry TYPE plifz,   "计划交货时间
  marc_gr_pr_time TYPE webaz,   "收货处理时间
  marc_safety_stk TYPE eisbe,   "安全库存
  marc_sm_key TYPE fhori,       "计划边际码
  marc_plan_strgp TYPE strgp,   "策略组
  marc_alt_bom_id TYPE altsl,   "选择方法
  stge_loc TYPE lgort_d,        "库存地点,占位，不用取出赋值，同上边库存地点
  mbew_val_class TYPE bklas,    "评估类
  mbew_price_ctrl TYPE vprsv,   "价格控制
  mbew_price_unit TYPE peinh,   "价格单位
  mbew_moving_pr TYPE verpr_bapi,"移动平均价
  mbew_std_price TYPE stprs_bapi,"标准价格
  mbew_qty_struct TYPE ck_ekalrel,"用QS的成本估算
  mbew_orig_mat TYPE hkmat,      "物料来源
  END OF str_material.
DATA: it_material LIKE TABLE OF str_material.
"$. Endregion Structure"
START-OF-SELECTION.
  PERFORM prepare_data.
  PERFORM excute_bapi.
*&---------------------------------------------------------------------*
*&      Form  PREPARE_DATA
*&---------------------------------------------------------------------*
FORM prepare_data .
  DATA : lv_matnr TYPE char18.
  itab-head_material = '1500-620'.
  itab-head_ind_sector = 'M'.
  itab-head_matl_type = 'HIBE'.        " 物料类型 "
  itab-mara_base_uom = 'CSE'.           " 基本计量单位 "
  itab-mara_matl_group = '010'.      " 物料组 "
  itab-t_old_mat_no = 'test007'.       " 型号 "
  itab-mara_division = '00'.           " 产品组 "
  itab-t_dsn_office = '001'.           " 实验室 / 办公室 "
  itab-mara_item_cat = 'NORM'.         " 普通项目组类别 "
  itab-mara_net_weight = 2.            " 净重 "
*  itab-mara_normt = ''.               " 行业标准描述 "
  itab-mara_size_dim = '2*3'.          " 大小 / 量纲 "
  itab-t_marm_gross_wt = 22 / 10.      " 毛重 "
  itab-mara_unit_of_wt = 'KG'.         " 重量单位 "
  itab-t_makt_matl_desc = '10W40 MOTOR OIL CASE test'.  " 物料描述 "
  CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
    EXPORTING
      input  = itab-head_material
    IMPORTING
      output = itab-head_material.
  lv_matnr = itab-head_material.
  TRANSLATE lv_matnr TO UPPER CASE.
  itab-head_material = lv_matnr.
  APPEND itab.
ENDFORM.                    " PREPARE_DATA "
*&---------------------------------------------------------------------*
*&      Form  EXCUTE_BAPI
*&---------------------------------------------------------------------*
FORM excute_bapi .
  LOOP AT itab.
    CLEAR: gs_bapimathead,gs_bapi_mara,gs_bapi_marax,gs_bapi_marc,
    gs_bapi_marcx,gs_bapi_mbew,gs_bapi_mbewx,gs_return.
    CLEAR: gt_bapi_makt,gt_bapi_marm,gt_bapi_marmx,gt_bapi_mlan,gt_return1.
    REFRESH:gt_bapi_makt,gt_bapi_marm,gt_bapi_marmx,gt_bapi_mlan,gt_return1.
    " 表头控制数据 "
    gs_bapimathead-material   = itab-head_material.  " 物料编码 "
    gs_bapimathead-matl_type  = itab-head_matl_type. " 物料类型 "
    gs_bapimathead-ind_sector = itab-head_ind_sector." 行业领域 "
    gs_bapimathead-basic_view = 'X'.      " 基本数据视图 "
    gs_bapimathead-purchase_view = 'X'.   " 采购视图 "
    gs_bapimathead-mrp_view = 'X'.        " MRP数据视图 "
*   gs_bapimathead-work_sched_view = 'X'. " 工作计划 "
*   gs_bapimathead-quality_view    = 'X'. " 质量管理视图 "
*   gs_bapimathead-storage_view    = 'X'. " 工厂存储 "
*   gs_bapimathead-sales_view      = 'X'. " 销售 "
*   gs_bapimathead-account_view    = 'X'. " 会计 "
*   gs_bapimathead-cost_view       = 'X'. " 成本 "
    CLEAR: pr_unit,pr_unit2.
    PERFORM frm_unit USING itab-mara_base_uom CHANGING pr_unit.    " 基本单位 "
    PERFORM frm_unit USING itab-mara_unit_of_wt CHANGING pr_unit2. " 重量单位 "
    " 基本数据视图数据 "
    gs_bapi_mara-base_uom = pr_unit.                 " 基本计量单位 "
    gs_bapi_mara-matl_group = itab-mara_matl_group.  " 物料组 "
    gs_bapi_mara-old_mat_no = itab-t_old_mat_no.     " 型号 "
    gs_bapi_mara-division = itab-mara_division.      " 产品组 "
    gs_bapi_mara-dsn_office = itab-t_dsn_office.     " 实验室 / 办公室 "
    gs_bapi_mara-item_cat = itab-mara_item_cat.      " 普通项目组类别 "
    gs_bapi_mara-unit_of_wt = pr_unit2.              " 重量单位 "
    gs_bapi_mara-net_weight = itab-mara_net_weight.  " 净重 "
    gs_bapi_mara-size_dim = itab-mara_size_dim.      " 大小 / 量纲 "
    gs_bapi_marax-base_uom = 'X'.
    gs_bapi_marax-matl_group = 'X'.
    gs_bapi_marax-old_mat_no = 'X'.
    gs_bapi_marax-division = 'X'.
    gs_bapi_marax-dsn_office = 'X'.
    gs_bapi_marax-item_cat = 'X'.
    gs_bapi_marax-unit_of_wt = 'X'.
    gs_bapi_marax-net_weight = 'X'.
    gs_bapi_marax-size_dim = 'X'.
    " 计量单位 "
    gt_bapi_marm-alt_unit = pr_unit.    " 替换单位 (必须为基本计量单位，否则会报错：没有转换因子) "
    gt_bapi_marm-numerator = 1.         " 分子 "
    gt_bapi_marm-denominatr = 1.        " 分母 "
    gt_bapi_marm-gross_wt = itab-t_marm_gross_wt. " 毛重 "
    gt_bapi_marm-unit_of_wt = pr_unit2. " 填充毛重时，注意此处需要添加重量单位，否则会提示没有指定单位 "
    APPEND gt_bapi_marm.
    CLEAR gt_bapi_marmx.
    gt_bapi_marmx-alt_unit = pr_unit.   " 注意此处不是填充 'X' "
    gt_bapi_marmx-numerator = 'X'.
    gt_bapi_marmx-denominatr = 'X'.
    gt_bapi_marmx-gross_wt = 'X'.
    gt_bapi_marmx-unit_of_wt = 'X'.     " 此处填充 'X' "
    IF gt_bapi_marmx-alt_unit IS NOT INITIAL AND gt_bapi_marmx-numerator IS NOT INITIAL
    AND gt_bapi_marmx-denominatr IS NOT INITIAL.
      APPEND gt_bapi_marmx.
    ENDIF.
    " 物料描述 "
    gt_bapi_makt-langu_iso = 'EN'.
    gt_bapi_makt-matl_desc = itab-t_makt_matl_desc.
    APPEND gt_bapi_makt.
    " 维护增强字段 "
    gs_bapi_te_mara-material   = itab-head_material.
    gs_bapi_te_mara-zz_model   = itab-zz_model.
    gs_bapi_te_mara-zz_drawing = itab-zz_drawing.
    gs_bapi_te_mara-zz_color   = itab-zz_color.
    l_valuepart = gs_bapi_te_mara.
    gt_extensionin-structure = 'BAPI_TE_MARA'.
    gt_extensionin-valuepart1 = l_valuepart+0(240).
    gt_extensionin-valuepart2 = l_valuepart+240(240).
    gt_extensionin-valuepart3 = l_valuepart+480(240).
    APPEND gt_extensionin.
    gs_bapi_te_marax-material  = itab-matnr.
    gs_bapi_te_marax-zz_model  = 'X'.
    gs_bapi_te_marax-zz_drawing_no = 'X'.
    gs_bapi_te_marax-zz_color = 'X'.
    l_valuepartx = gs_bapi_te_marax.
    gt_extensioninx-structure = 'BAPI_TE_MARAX'.
    gt_extensioninx-valuepart1 = l_valuepartx+0(240).
    gt_extensioninx-valuepart2 = l_valuepartx+240(240).
    gt_extensioninx-valuepart3 = l_valuepartx+480(240).
    APPEND gt_extensioninx.
    " CALL BAPI "
    CALL FUNCTION 'BAPI_MATERIAL_SAVEDATA'
      EXPORTING
        headdata            = gs_bapimathead
        clientdata          = gs_bapi_mara
        clientdatax         = gs_bapi_marax
      IMPORTING
        return              = gs_return
      TABLES
        materialdescription = gt_bapi_makt[]
        unitsofmeasure      = gt_bapi_marm[]
        unitsofmeasurex     = gt_bapi_marmx[]
        returnmessages      = gt_return1[].
    IF gs_return-type NE 'E'.
      CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
        EXPORTING
          wait = 'X'.
    ELSE.
      CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
    ENDIF.
  ENDLOOP.
  WRITE : gs_return-type,gs_return-message.
ENDFORM.                    " EXCUTE_BAPI "
*&---------------------------------------------------------------------*
*&      Form  frm_unit
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->UNIT1      text
*      -->UNIT2      text
*----------------------------------------------------------------------*
FORM frm_unit USING unit1 CHANGING unit2.
  CALL FUNCTION 'CONVERSION_EXIT_CUNIT_INPUT'
    EXPORTING
      input          = unit1
      language       = sy-langu
    IMPORTING
      output         = unit2
    EXCEPTIONS
      unit_not_found = 1
      OTHERS         = 2.
  IF sy-subrc <> 0.
*   message id sy-msgid type sy-msgty number sy-msgno
*           with sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    "FRM_UNIT"
```

