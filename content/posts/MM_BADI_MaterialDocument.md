---
title: "收货或者货物移动(MIGO,MB11,MB1A)增强"
date: 2019-08-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BADI

---



### BADI：MB_DOCUMENT_BADI 使用

![MB_DOCUMENT_BADI](/images/MM/BADI-MB_DOCUMENT_BADI_0.png)

该 BADI 有两个方法：MB_DOCUMENT_BEFORE_UPDATE、MB_DOCUMENT_UPDATE。

- 假如想在点击保存按钮的时候根据生成的凭证号（即表示这个增强点处凭证号已经生成），把某些数据更新到你的自建表的话，要用第二个方法 MB_DOCUMENT_UPDATE。
- 调试可以在第一个方法 MB_DOCUMENT_BEFORE_UPDATE 里面调试。第二个方法打断点是无法进入的，但是这个方法确实有执行，所以说只能在第一个方法那边调试。
- 注意不能在方法 MB_DOCUMENT_BEFORE_UPDATE 里面写 COMMIT WORK。

### 实现该 BADI 接口，并重写其方法

![MB_DOCUMENT_BADI](/images/MM/BADI-MB_DOCUMENT_BADI_1.png)

```ABAP
 method if_ex_mb_document_badi~mb_document_update. 
 data: gs_mseg type  mseg. 
 datal: gt_gdfh type table of ztmm_gdfh. 
 data: gs_gdfh type ztmm_gdfh. 
 data: gt_gdth type table of ztmm_gdth. 
 data: gs_gdth type ztmm_gdth. 
 data: gt_lxjj type table of ztmm_lxjjgl. 
 data: gs_lxjj type ztmm_lxjjgl. 
 data: gs_mkpf type mkpf. 
 if sy-tcode eq ‘MIGO’ or sy-tcode  eq ‘MB11′. 
  read table xmkpf into gs_mkpf index 1. 
  loop at xmseg into gs_mseg. 
   if  gs_mseg-bwart eq ‘201′ and gs_mseg-sobkz eq ” . 
    select single zgngo into gs_lxjj-zlxjjbm from marc where matnr eq gs_mseg-matnr and werks eq gs_mseg-werks. 
    if sy-subrc eq 0. 
     gs_lxjj-mblnr = gs_mseg-mblnr. 
     gs_lxjj-matnr = gs_mseg-matnr. 
     gs_lxjj-werks = gs_mseg-werks. 
     gs_lxjj-bldat = gs_mkpf-bldat. 
     append gs_lxjj to gt_lxjj. 
    endif. 
   endif. 
   if gs_mseg-werks eq ‘SD00′ and ( ( gs_mseg-bwart eq ‘201′ and gs_mseg-sobkz eq ” )  or  ( gs_mseg-bwart eq ‘201′ and gs_mseg-sobkz eq ‘K’ ) 
   or ( gs_mseg-bwart eq ‘221′ and gs_mseg-sobkz eq ‘Q’ ) or ( gs_mseg-bwart eq ‘251′ and gs_mseg-sobkz eq ” ) 
   or ( gs_mseg-bwart eq ‘251′ and gs_mseg-sobkz eq ‘K’ ) or ( gs_mseg-bwart eq ‘221′ and gs_mseg-sobkz eq ‘K’ ) ). 
\*    BREAK ZZWANGLP. 
    gs_gdfh-mblnr = gs_mseg-mblnr. 
    gs_gdfh-mjahr = gs_mseg-mjahr. 
    gs_gdfh-bldat = gs_mkpf-bldat. 
    gs_gdfh-budat = gs_mkpf-budat. 
    gs_gdfh-cpudt = gs_mkpf-cpudt. 
    gs_gdfh-cputm = gs_mkpf-cputm. 
    gs_gdfh-mblpo = gs_mseg-zeile. 
    gs_gdfh-matnr = gs_mseg-matnr. 
    gs_gdfh-menge = gs_mseg-menge. 
    gs_gdfh-charg = gs_mseg-charg. 
    gs_gdfh-lgort = gs_mseg-lgort. 
    gs_gdfh-lgpla = gs_mseg-lgpla. 
    gs_gdfh-dmbtr = gs_mseg-dmbtr. 
    gs_gdfh-pspel  = gs_mseg-ps_psp_pnr. 
    gs_gdfh-zdj  = gs_gdfh-dmbtr / gs_gdfh-menge.“单价=（本位币金额/发放数量） 
**序列号：通过MBLNR&MJAHR& MBLPO在表SER03中找到OBKNR，再通过OBKNR在表OBJK找到SERNR作为序列号更新到表ZTMM_GDFH 
\*     SELECT SINGLE SERNR INTO GS_GDFH-SERNR FROM OBJK 
\*      JOIN SER03 ON SER03~OBKNR = OBJK~OBKNR 
\*     WHERE MBLNR EQ GS_MSEG-MBLNR AND MJAHR EQ GS_MSEG-MJAHR AND ZEILE EQ GS_MSEG-ZEILE. 
\*    供应商：通过CHARG在表MCHA找到LIFNR 
    select single lifnr into gs_gdfh-lifnr from mcha where charg eq gs_gdfh-charg and matnr eq gs_gdfh-matnr and werks eq ‘SD00′. 
\*    合同号：通过CHARG在表EKBE中找到EBELN，再通过EBELN在表EKKO找到SUBMI。 
    select single submi into gs_gdfh-submi from ekko 
     join ekbe on ekbe~ebeln = ekko~ebeln 
    where charg eq gs_gdfh-charg and matnr eq gs_gdfh-matnr and werks eq ‘SD00′. 


\*    采购订单及行号：通过CHARG在表EKBE中找到EBELN，再通过EBELN在表EKPO找到EKPO-EBELP。 
    select single ekbe~ebeln ebelp into (gs_gdfh-ebeln,gs_gdfh-ebelp) 
     from ekko 
     join ekbe on ekbe~ebeln = ekko~ebeln 
     where charg eq gs_gdfh-charg and matnr eq gs_gdfh-matnr and werks eq ‘SD00′.“EBELP EQ GS_MSEG-ZEILE. 

\*   依据预留编号及预留项目编号作为关键字段，在表ZTMM_GDXX找到工单、作业工种（工序）、工单行号字段 
    select single zgd zgx zhxmbh into (gs_gdfh-zgd,gs_gdfh-zgx,gs_gdfh-zgdhh) from ztmm_gdxx where rsnum eq gs_mseg-rsnum and rspos eq gs_mseg-rspos. 
    if sy-subrc ne 0. 
     clear gs_gdfh. 
    else. 
     gs_gdfh-zyfh = ‘X’. 
     append gs_gdfh to gt_gdfh. 
    endif. 
   endif. 
   if gs_mseg-werks eq ‘SD00′ and ( ( gs_mseg-bwart eq ‘202′ and gs_mseg-sobkz eq ” ) or ( gs_mseg-bwart eq ‘202′ and gs_mseg-sobkz eq ‘K’ )  or  ( gs_mseg-bwart eq ‘222′ and gs_mseg-sobkz eq ‘Q’ ) 
     or ( gs_mseg-bwart eq ‘252′ and gs_mseg-sobkz eq ” ) or ( gs_mseg-bwart eq ‘222′ and gs_mseg-sobkz eq ‘K’ ) 
     or ( gs_mseg-bwart eq ‘252′ and gs_mseg-sobkz eq ‘K’ ) ).“OR ( GS_MSEG-BWART EQ ’242′ AND GS_MSEG-SOBKZ EQ ” )dete by libin 20120516 bc songwenbin 
\*    BREAK ZZWANGLP. 
    gs_gdth-mblnr = gs_mseg-mblnr. 
    gs_gdth-mjahr = gs_mseg-mjahr. 
    gs_gdth-bldat = gs_mkpf-bldat. 
    gs_gdth-budat = gs_mkpf-budat. 
    gs_gdth-cpudt = gs_mkpf-cpudt. 
    gs_gdth-cputm = gs_mkpf-cputm. 
    gs_gdth-mblpo = gs_mseg-zeile. 
    gs_gdth-matnr = gs_mseg-matnr. 
    gs_gdth-menge = gs_mseg-menge. 
    gs_gdth-charg = gs_mseg-charg. 
    gs_gdth-lgort = gs_mseg-lgort. 
    gs_gdth-lgpla = gs_mseg-lgpla. 
    gs_gdth-dmbtr = gs_mseg-dmbtr. 
    gs_gdth-pspel  = gs_mseg-ps_psp_pnr. 
    gs_gdth-zdj  = gs_gdth-dmbtr / gs_gdth-menge.“单价=（本位币金额/发放数量） 
**序列号：通过MBLNR&MJAHR& MBLPO在表SER03中找到OBKNR，再通过OBKNR在表OBJK找到SERNR作为序列号更新到表ZTMM_GDFH 
\*     SELECT SINGLE SERNR INTO GS_GDFH-SERNR FROM OBJK 
\*      JOIN SER03 ON SER03~OBKNR = OBJK~OBKNR 
\*     WHERE MBLNR EQ GS_MSEG-MBLNR AND MJAHR EQ GS_MSEG-MJAHR AND ZEILE EQ GS_MSEG-ZEILE. 
\*   依据预留编号及预留项目编号作为关键字段，在表ZTMM_GDXX找到工单、作业工种（工序）、工单行号字段 
    select single zgd zgx zhxmbh into (gs_gdth-zgd,gs_gdth-zgx,gs_gdth-zgdhh) from ztmm_gdxx where rsnum eq gs_mseg-rsnum and rspos eq gs_mseg-rspos. 
    if sy-subrc ne 0. 
     clear gs_gdfh. 
    else. 
     gs_gdth-zyfh = ‘X’. 
     append gs_gdth to gt_gdth. 
    endif. 
   endif. 
  endloop. 
  if gt_gdfh is not initial. 
   modify ztmm_gdfh from table gt_gdfh. 
  endif. 
  if gt_gdth is not initial. 
   modify ztmm_gdth from table gt_gdth. 
  endif. 
  if gt_lxjj is not initial. 
   modify ztmm_lxjjgl from table gt_lxjj. 
  endif. 
 endif. 
endmethod.
```


