---
title: "查找增强程序2"
date: 2018-09-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---



##    查找增强出口和BADI

```js
*&---------------------------------------------------------------------*
*& Report  Z_FIND_EXIT_AND_BADI
*&
*&---------------------------------------------------------------------*
*&
*&
*&---------------------------------------------------------------------*
report z_find_exit_and_badi no standard page heading.
*&---------------------------------------------------------------------*
*&  Enter the transaction code that you want to search through in order
*&  to find which Standard SAP User Exits and BADIs
*&
*&---------------------------------------------------------------------*
*& For field 'SUBC' of table 'TRDIR':
*&   M  Module Pool
*&   F  Function group
*&   S  Subroutine Pool
*&   J  Interface pool
*&   K  Class pool
*&   T  Type Pool
*&   X  XSLT Program
*&---------------------------------------------------------------------*
*& Tables
*&---------------------------------------------------------------------*
tables: tstc     , " SAP Transaction Codes
  tadir    , " Directory of Repository Objects
  modsapt  , " SAP Enhancements - Short Texts
  sxs_attrt, " SAP BADI - short text
  modact   , " Modifications
  trdir    , " System table TRDIR
  tfdir    , " Function Module
  enlfdir  , " Additional Attributes for Function Modules
  tstct    . " Transaction Code Texts

*&---------------------------------------------------------------------*
*& Variables
*&---------------------------------------------------------------------*
data: jtab        like tadir occurs 0 with header line.
data: field1(30).
data: v_devclass  like tadir-devclass.
data: object      like tadir-object.
data: bdcdata_wa  type bdcdata,
      bdcdata_tab type table of bdcdata.
data: opt         type ctu_params.

*&---------------------------------------------------------------------*
*& Selection Screen Parameters
*&---------------------------------------------------------------------*
selection-screen begin of block a01 with frame title text-001.
  selection-screen skip.
  parameters: p_tcode like tstc-tcode obligatory.
  selection-screen skip.
  parameters: exit radiobutton group 1 default 'X',
  badi radiobutton group 1.
selection-screen end of block a01.

define bdc_program.
  clear bdcdata_wa.
  bdcdata_wa-program  = &1.
  bdcdata_wa-dynpro   = &2.
  bdcdata_wa-dynbegin = &3.
  append bdcdata_wa to bdcdata_tab.
end-of-definition.
define bdc_detail.
  clear bdcdata_wa.
  bdcdata_wa-fnam = &1.
  bdcdata_wa-fval = &2.
  append bdcdata_wa to bdcdata_tab.
end-of-definition.

*&---------------------------------------------------------------------*
*& Start of main program
*&---------------------------------------------------------------------*
start-of-selection.
if exit = 'X'.
  object = 'SMOD'.  " User-exit!
else.
  object = 'SXSD'.  " BADI!
endif.

* Validate Transaction Code:
select single * from tstc where tcode eq p_tcode.
* Find Repository Objects for transaction code:
if sy-subrc eq 0.                                         " IF 1
  select single * from tadir where pgmid    = 'R3TR'
  and object   = 'PROG'
  and obj_name = tstc-pgmna."Program
*    name!
  move: tadir-devclass to v_devclass. " Package
  if sy-subrc ne 0.
    select single * from trdir where name = tstc-pgmna.
    if trdir-subc eq 'F'.  " Function Group
      select single * from tfdir   where pname    = tstc-pgmna.
      select single * from enlfdir where funcname = tfdir-funcname.
      select single * from tadir   where pgmid    = 'R3TR'
      and object   = 'FUGR'
      and obj_name = enlfdir-area.
      move: tadir-devclass to v_devclass.
    endif.
  endif.

*   Find SAP Modifactions:
  select * from tadir into table jtab where pgmid    = 'R3TR'
*                                          AND object   = 'SMOD'
  and object   = object
  and devclass = v_devclass.
  select single * from tstct where sprsl eq sy-langu
  and tcode eq p_tcode.

  format color col_positive intensified off.
  write: /(19)  'Transaction Code - ',
  20(20) p_tcode,
  45(50) tstct-ttext.
  skip.
  if not jtab[] is initial.                               " IF 2
    write: /(95) sy-uline.
    format color col_heading intensified on.
*     Exit:
    if exit = 'X'.
      write: /1  sy-vline,
 'Exit Name',
sy-vline ,
'Description',
sy-vline.
*     BADI:
    else.
      write: /1  sy-vline,
 'BADI Name',
sy-vline ,
'Description',
sy-vline.
    endif.
    write:/(95) sy-uline.
    loop at jtab.
*       EXIT:
      if exit = 'X'.
        select single * from modsapt where sprsl = sy-langu
        and name  = jtab-obj_name.
        format color col_normal intensified off.
        write: /1  sy-vline,
 jtab-obj_name hotspot on,
sy-vline ,
modsapt-modtext,
sy-vline.
*       BADI:
      else.
        select single * from sxs_attrt where sprsl     = sy-langu
        and exit_name =
        jtab-obj_name.
        format color col_normal intensified off.
        write: /1  sy-vline,
 jtab-obj_name hotspot on,
sy-vline ,
sxs_attrt-text,
sy-vline.
      endif.
    endloop.
    write: /(95) sy-uline.
    describe table jtab.
    skip.
    format color col_total intensified on.
    if exit = 'X'.
      write: / 'No of Exits:', sy-tfill.
    else.
      write: / 'No of BADIs:', sy-tfill.
    endif.
  else.                                                   " IF 2
    format color col_negative intensified on.
    write: /(95) 'No User Exit exists'.
  endif.                                                  " IF 2
else.                                                     " IF 1
  format color col_negative intensified on.
  write: /(95) 'Transaction Code Does Not Exist'.
endif.                                                    " IF 1
* Take the user to SMOD for the Exit that was selected:
    at line-selection.
    get cursor field field1.
    check field1(4) eq 'JTAB'.
* For exit:
    if exit = 'X'.
      set parameter id 'MON' field sy-lisel+1(10).
      call transaction 'SMOD' and skip first screen.
* For BADI:
    else.
      clear: bdcdata_wa, bdcdata_tab[].
      bdc_program 'SAPLSEXO' '0100' 'X'.
      bdc_detail 'BDC_CURSOR' 'G_IS_BADI'.
      bdc_detail 'BDC_OKCODE' '=ISSPOT'.
      bdc_detail 'G_IS_BADI' 'X'.
      bdc_program 'SAPLSEXO' '0100' 'X'.
      bdc_detail 'BDC_CURSOR' 'G_BADINAME'.
      bdc_detail 'BDC_OKCODE' '=SHOW'.
      bdc_detail 'G_BADINAME' sy-lisel+1(20).
      opt-dismode = 'E'.
      opt-defsize = 'X'.
      call transaction 'SE18' using bdcdata_tab options from opt.
    endif.
```


​     

