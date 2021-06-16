---
title: "批量创建/修改物料主数据"
date: 2020-08-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 程序实例

```
REPORT ZUPDATE_MATNR.
data: headdata      type bapimathead,     " 表头数据 "
      clientdata    type bapi_mara,       " 基本数据 "
      clientdatax   type bapi_marax.      " 变更数据标识 "
data: materialdescription type table of bapi_makt with header line.
data: unitsofmeasure type table of bapi_marm with header line.      
data: unitsofmeasurex type table of bapi_marmx with header line.    
data: pr_unit type meins,  " 基本单位 "
      pr_unit2 type meins, " 重量单位 "
      return type bapiret2.
data: begin of msg occurs 0,
  material type matnr,
  description type maktx,
  message(97) type c,
end of msg.
data:begin of itab occurs 0,
  head_material type matnr,      " 物料号 "
  head_ind_sector type mbrsh,    " 行业领域 "
  head_matl_type type mtart,     " 物料类型 "
  t_makt_matl_desc type maktx,   " 物料描述 "
  mara_base_uom type meins,      " 基本计量单位 "
  mara_matl_group type matkl,    " 物料组 "
  t_old_mat_no type bismt,       " 型号 "
  mara_division type spart,      " 产品组 "
  t_dsn_office type labor,       " 实验室 / 办公室 "
  mara_item_cat type mtpos_mara, " 普通项目组类别 "
  t_marm_gross_wt type brgew,    " 毛重 "
  mara_unit_of_wt type gewei,    " 重量单位 "
  mara_net_weight type ntgew,    " 净重 "
  mara_size_dim type groes,      " 大小 / 量纲 "
end of itab.
data itab1 type itab occurs 0 with header line.

PERFORM putdata.
PERFORM run.

FORM run.
loop at itab.
  clear headdata.
  headdata-material       = itab-head_material.
  headdata-matl_type      = itab-head_matl_type.
  headdata-ind_sector     = itab-head_ind_sector.
  headdata-basic_view     = 'X'.  " 基本数据视图 "
  clear pr_unit,pr_unit2.
  PERFORM frm_unit using itab-mara_base_uom changing pr_unit.    " 基本单位 "
  PERFORM frm_unit using itab-mara_unit_of_wt changing pr_unit2. " 重量单位 "
  clear clientdata.
  clientdata-base_uom = pr_unit.                 " 基本计量单位 "
  clientdata-matl_group = itab-mara_matl_group.  " 物料组 "
  clientdata-old_mat_no = itab-t_old_mat_no.     " 型号 "
  clientdata-division = itab-mara_division.      " 产品组 "
  clientdata-dsn_office = itab-t_dsn_office.     " 实验室 / 办公室 "
  clientdata-item_cat = itab-mara_item_cat.      " 普通项目组类别 "
  clientdata-unit_of_wt = pr_unit2.              " 重量单位 "
  clientdata-net_weight = itab-mara_net_weight.  " 净重 "
  clientdata-size_dim = itab-mara_size_dim.      " 大小 / 量纲 "
  " bapi_mara 的复选框结构 "
  clear clientdatax.
  clientdatax-base_uom = 'X'.    
  clientdatax-matl_group = 'X'.  
  clientdatax-old_mat_no = 'X'.  
  clientdatax-division = 'X'.    
  clientdatax-dsn_office = 'X'.  
  clientdatax-item_cat = 'X'.    
  clientdatax-unit_of_wt = 'X'.  
  clientdatax-net_weight = 'X'.  
  clientdatax-size_dim = 'X'.    
  " 计量单位
  unitsofmeasure-alt_unit = pr_unit.    " 替换单位 (必须为基本计量单位，否则会报错：没有转换因子) "
  unitsofmeasure-numerator = 1.         " 分子 "
  unitsofmeasure-denominatr = 1.        " 分母 "
  unitsofmeasure-gross_wt = itab-t_marm_gross_wt. " 毛重 "
  unitsofmeasure-unit_of_wt = pr_unit2. " 填充毛重时，注意此处需要添加重量单位，否则会提示没有指定单位 "
  append unitsofmeasure.
  clear unitsofmeasure.
  unitsofmeasurex-alt_unit = pr_unit.   " 注意此处不是填充 'X' "
  unitsofmeasurex-numerator = 'X'.
  unitsofmeasurex-denominatr = 'X'.
  unitsofmeasurex-gross_wt = 'X'.
  unitsofmeasurex-unit_of_wt = 'X'.     " 此处填充 'X' "
  if unitsofmeasurex-alt_unit is not initial and unitsofmeasurex-numerator is not initial 
                                             and unitsofmeasurex-denominatr is not initial.
    append unitsofmeasurex.
  endif.
  clear unitsofmeasurex.
  " 物料描述
  clear materialdescription[].
  materialdescription-langu_iso = 'ZH'.
  materialdescription-matl_desc = itab-t_makt_matl_desc.
  append materialdescription.
  clear return.
  call function 'BAPI_MATERIAL_SAVEDATA'
    exporting
      headdata            = headdata
      clientdata          = clientdata
      clientdatax         = clientdatax
    importing
      return              = return
    tables
      materialdescription = materialdescription[]
      unitsofmeasure = unitsofmeasure[]
      unitsofmeasurex = unitsofmeasurex[].
  if return-type ne 'E'.
    call function 'BAPI_TRANSACTION_COMMIT'
      exporting
        wait          = 'X' .
  else.
    call function 'bapi_transaction_rollback'.
  endif.
endloop.
write : return-type,return-message.
ENDFORM.
FORM putdata.
  itab-head_material = '10101010105'.
  itab-head_ind_sector = 'M'.          
  itab-head_matl_type = 'zroh'.        " 物料类型 "
  itab-mara_base_uom = 'EA'.           " 基本计量单位 "
  itab-mara_matl_group = '10235'.      " 物料组 "
  itab-t_old_mat_no = 'testbapi05'.    " 型号 "
  itab-mara_division = '00'.           " 产品组 "
  itab-t_dsn_office = '001'.           " 实验室 / 办公室 "
  itab-mara_item_cat = 'NORM'.         " 普通项目组类别 "
  itab-mara_net_weight = 2.            " 净重 "
*  itab-mara_normt = ''.               " 行业标准描述 "
  itab-mara_size_dim = '2*3'.          " 大小 / 量纲 "
  itab-t_marm_gross_wt = 22 / 10.      " 毛重 "
  itab-mara_unit_of_wt = 'KG'.         " 重量单位 "
  itab-t_makt_matl_desc = 'test mat'.  " 物料描述 "
  call function 'CONVERSION_EXIT_ALPHA_INPUT'"
    exporting
      input  = itab-head_material
    importing
      output = itab-head_material.
  append itab.
ENDFORM.
* 获取基本计量单位内码
FORM frm_unit using unit1 changing unit2.
  call function 'CONVERSION_EXIT_CUNIT_INPUT'
    exporting      
      input          = unit1
      language       = sy-langu
    importing      
      output         = unit2
    exceptions      
      unit_not_found = 1
      others         = 2.
  if sy-subrc <> 0.
*   message id sy-msgid type sy-msgty number sy-msgno
*           with sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  endif.
ENDFORM.                    "frm_unit
```

