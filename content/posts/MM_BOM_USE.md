---
title: " BOM 展开 "
date: 2018-11-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BOM

---


## SAP BOM读取逻辑

### 相关表

| Table | Description                           | Table | Description                         |
| :---- | :------------------------------------ | :---- | :---------------------------------- |
| MAST  | Material BOM                          | PLKO  | Routing Group Header                |
| STKO  | BOM Header                            | PLSO  | Routing Group Sequence              |
| STPO  | BOM Positions (detail)                | PLPO  | Routing Group Operations            |
| MAPL  | Assignment fo Task Lists to Materials | AFKO  | Production Order Header             |
| STZU  | Permanent BOM data                    | AFPO  | Production Order Position (details) |


可用函数：`CSAP_MAT_BOM_READ`

### 读取逻辑 ###

#### 查找 Material，Plant，Material Desc

TABLE：MARA、MARC、MAKT

```ABAP
MARA-MATNR    
MARC-WERKS   
MARA-MTART (Material type)
Note: Proceeding to Step 2 or 3 or 4 depends on the input radio button for 
      Production or Engineering or ALL BOM option.
```

#### 查找 BOM - Engineering

TABLE：MAST、STKO、STOP、STZU

```ABAP
Material (MAST-MATNR) = Materials selected above
  AND Plant (MAST–WERKS) = As in input 
  AND BOM usage (MAST-STLAN) = 2 (Engineering usage).
Read BOM Header and Item
      Read BOM Header from table  [STKO].
          BOM Number (STKO-STLNR) = BOM number got in previous step and
          BOM alternative (STKO-STLAL) = BOM alternative got in previous step.
      Read BOM Item details from  [STOP].
          BOM number (STOP-STLNR) = STKO-STNLR.
      Read BOM text from table  STZU.
```

#### 查找 BOM – Production

TABLE：MAST、STKO、STOP、STZU、MKAL

```ABAP
Material (MAST-MATNR) = Materials selected above AND Plant (MAST–WERKS) = As in input 
  AND BOM usage (MAST-STLAN) = 1 (Production usage).
Read BOM Header and Item
  Read BOM Header from table  [STKO].
    BOM Number (STKO-STLNR) = BOM number got in previous step
    AND BOM alternative (STKO-STLAL) = BOM alternative got in previous step.
  Read BOM Item details from  [STOP].
    BOM number (STOP-STLNR) = STKO-STNLR.
  Read BOM text from table STZU.
Search for Resource / Production Version (Production BOM’s only)   [MKAL]
  Material number (MKAL–MATNR) = Material number from above selection (MAST) 
    And Plant (MKAL–WERKS) = Plant from above selection (MAST)
    And Alternative BOM (MKAL-STLAL) = Alternative BOM from above selection (MAST) 
    And  BOM Usage (MKAL-STLAN) = 1 (Production BOM).
Note:
1. If no production version exists for any of the BOM’s write such records at the bottom of 
   the report under the heading “No Production Version Exists (Production BOM’s)”.
2. Sort the output on Plant, Usage and then on Material.
3. If multiple plants then the report will be displayed Plant wise.
   • Header to be displayed for Production / Engineering BOM option.
4. Material BOM Comparison.  [MAST]
   Material (MAST-MATNR) = Materials selected above AND Plant (MAST–WERKS) = As in input.
   ● Read BOM Header and Item
  	 Read BOM Header from table [STKO].
    	  BOM Number (STKO-STLNR) = BOM number got in previous step and
    	  BOM alternative (STKO-STLAL) = BOM alternative got in previous step.
  	 Read BOM Item details from [STOP].
          BOM number (STOP-STLNR) = STKO-STNLR.
```

## BOM逆向和正向查询 ##

### 顺查BOM（CS12）

`CS_BOM_EXPL_MAT_V2`：capid 参数，一般情况下所取的 BOM 都是生产用 BOM（capid = PP01）。

- PP01：Production - general 
- BEST：Inventory management
- INST：Plant maintenance
- PC01：Costing
- PI01：Process manufacturing
- SD01：Sales and distribution

```ABAP
DATA: selpool TYPE TABLE OF cstmat WITH HEADER LINE.
DATA: dstst_flg LIKE csdata-xfeld.
DATA: matcat TYPE TABLE OF cscmat WITH HEADER LINE.
DATA: stb TYPE STANDARD TABLE OF stpox WITH HEADER LINE.
DATA: return TYPE TABLE OF bapireturn WITH HEADER LINE.

CALL FUNCTION 'CS_BOM_EXPL_MAT_V2'
  EXPORTING
    capid = p_capid      "BOM应用程序，应用程序一般为PP01"
    datuv = p_datuv      "有效开始日通常为系统的当前日期"
    emeng = menge        "需求数量"
    mtnrv = p_matnr      "物料专用号，要展开BOM的物料"
    mehrs = 'X'          "x表示多层展开﹐space表示只展开第一层"
    werks = pm_werks     "工厂"
    stlan = xxx          "BOM用途"
    stlal = xxxx         "BOM可选"
  IMPORTING
    topmat = selpool     "抬头明细"
    dstst  = dstst_flg
  TABLES
    stb = stb           "展开的BOM存放在该内表"
    matcat = matcat     "下面含有元件的物料存放在该内表"
"获取物料BOM清单 Items"
CALL FUNCTION 'CSAP_MAT_BOM_ITEM_SELECT'
  EXPORTING
    i_stpo                     = ls_i_stpo
    material                   = lv_i_matnr
    plant                      = lv_i_plant
    bom_usage                  = lv_i_usage
    alternative                = lv_i_alter
    fl_material_check          = lc_check
    fl_foreign_key_check       = lc_check
    valid_from                 = lv_i_datuv
    valid_to                   = lv_i_datub
  TABLES
    t_stpo                     = lt_e_stpo
  EXCEPTIONS
    error                      = error_msg
    others                     = error_other.
```

### 逆查 BOM(CS15)：取物料的上层物料

`CS_WHERE_USED_MAT`

```ABAP
DATA: IT_WULTB LIKE STPOV OCCURS 0 WITH HEADER LINE,
      IT_EQUICAT LIKE CSCEQUI OCCURS 0 WITH HEADER LINE,
      IT_KNDCAT LIKE CSCKND OCCURS 0 WITH HEADER LINE,
      IT_MATCAT LIKE CSCMAT OCCURS 0 WITH HEADER LINE,
      IT_STDCAT LIKE CSCSTD OCCURS 0 WITH HEADER LINE,
      IT_TPLCAT LIKE CSCTPL OCCURS 0 WITH HEADER LINE,
      IT_PRJCAT LIKE CSCPRJ OCCURS 0 WITH HEADER LINE.
  CLEAR:IT_WULTB,IT_WULTB[].
  CALL FUNCTION 'CS_WHERE_USED_MAT'
    EXPORTING
      DATUB        = SY-DATUM    "有效日期To"
      DATUV        = SY-DATUM    "有效日期From"
      MATNR        = P_MATNR   "物料号"
*     POSTP        = ' '
*     RETCODE_ONLY = ' '
*     STLAN        = ' '         "BOM用途"
*     MCLMT        = '00000000'
      WERKS        = P_WERKS
*    IMPORTING
*    TOPMAT        =
    TABLES
       WULTB       = IT_WULTB    "上层物料明细，包含编码、编码描述、上层需求数量、子件需求数量等"
       EQUICAT     = IT_EQUICAT
       KNDCAT      = IT_KNDCAT
       MATCAT      = IT_MATCAT   "反查出的子件上层物料明细（按物料号汇总后的结果）"
       STDCAT      = IT_STDCAT
       TPLCAT      = IT_TPLCAT
    EXCEPTIONS
       CALL_INVALID               = 1
       MATERIAL_NOT_FOUND         = 2
       NO_WHERE_USED_REC_FOUND    = 3
       NO_WHERE_USED_REC_SELECTED = 4
       NO_WHERE_USED_REC_VALID    = 5
       OTHERS                     = 6.
```

**T-Code：CS02** :确保递归 BOM 的比例在 0.95 左右。即：产品 A，其 BOM 中的 A 的消耗量应该小于 0.95. 

### 创建 BOM

BAPI_MATERIAL_BOM_GROUP_CREATE：可以创建 BOM 组

CSAP_MAT_BOM_CREATE：创建 BOM

CSAP_MAT_BOM_MAINTAIN：维护 BOM，创建可选的 BOM 会有问题

### 批量删除 BOM 分配

```ABAP
CALL FUNCTION 'CSAP_MAT_BOM_ALLOC_DELETE'
  EXPORTING
    MATERIAL                 = ITAB-MATNR
    PLANT                    = ITAB-WERKS
    BOM_USAGE                = '1'  "BOM 用途"
    ALTERNATIVE              =
    FL_NO_CHANGE_DOC         = ' '
    FL_COMMIT_AND_WAIT       = ' '
  IMPORTING
    FL_WARNING               = FLG_WARNING
    BOM_NO                   = BOM_NO
  TABLES
    T_PLANT                  =
  EXCEPTIONS
    ERROR                    = 1
    OTHERS                   = 2.
IF FLG_WARNING = 'X'.
  WRITE :/ ITAB-WERKS,ITAB-MATNR , '删除成功'.
ELSE.
  WRITE :/ ITAB-WERKS,ITAB-MATNR , '删除失败'.
ENDIF.
```





