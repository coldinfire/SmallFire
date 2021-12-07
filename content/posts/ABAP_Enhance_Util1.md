---
title: " 查找增强程序(1) "
date: 2018-09-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---

###    查找增强出口和BADI

```ABAP
*&---------------------------------------------------------------------*
*& Report  Z_FIND_EXIT_AND_BADI
*&---------------------------------------------------------------------*
report z_find_exit_and_badi no standard page heading.
*&---------------------------------------------------------------------*
*&  Enter the transaction code that you want to search through in order
*&  to find which Standard SAP User Exits and BADIs
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
*Tables
TABLES: tstc     , " SAP Transaction Codes "
        tadir    , " Directory of Repository Objects "
        modsapt  , " SAP Enhancements - Short Texts "
        sxs_attrt, " SAP BADI - short text "
        modact   , " Modifications "
        trdir    , " System table TRDIR "
        tfdir    , " Function Module "
        enlfdir  , " Additional Attributes for Function Modules "
        tstct    . " Transaction Code Texts "
*Variables
DATA: jtab        LIKE tadir OCCURS 0 WITH HEADER LINE.
DATA: field1(30).
DATA: v_devclass  LIKE tadir-devclass.
DATA: object      LIKE tadir-object.
DATA: bdcdata_wa  TYPE bdcdata,
      bdcdata_tab TYPE TABLE OF bdcdata.
DATA: opt         TYPE ctu_params.
*Selection Screen Parameters
SELECTION-SCREEN BEGIN OF BLOCK a01 WITH FRAME TITLE text-001.
SELECTION-SCREEN SKIP.
PARAMETERS: p_tcode LIKE tstc-tcode OBLIGATORY.
SELECTION-SCREEN SKIP.
PARAMETERS: exit RADIOBUTTON GROUP 1 DEFAULT 'X',
            badi RADIOBUTTON GROUP 1.
SELECTION-SCREEN END OF BLOCK a01.
*BDC
DEFINE bdc_program.
  clear bdcdata_wa.
  bdcdata_wa-program  = &1.
  bdcdata_wa-dynpro   = &2.
  bdcdata_wa-dynbegin = &3.
  append bdcdata_wa to bdcdata_tab.
END-OF-DEFINITION.
DEFINE bdc_detail.
  clear bdcdata_wa.
  bdcdata_wa-fnam = &1.
  bdcdata_wa-fval = &2.
  append bdcdata_wa to bdcdata_tab.
END-OF-DEFINITION.
*Start of main program
START-OF-SELECTION.
  IF exit = 'X'.
    object = 'SMOD'.  " User-exit! "
  ELSE.
    object = 'SXSD'.  " BADI! "
  ENDIF.
* Validate Transaction Code:
  SELECT SINGLE * FROM tstc WHERE tcode EQ p_tcode.
* Find Repository Objects for transaction code:
  IF sy-subrc EQ 0.                                         "IF 1"
    SELECT SINGLE * FROM tadir
     WHERE pgmid = 'R3TR'
       AND object   = 'PROG'
       AND obj_name = tstc-pgmna.       "Program name!"
    MOVE: tadir-devclass TO v_devclass. "Package"
    IF sy-subrc NE 0.
      SELECT SINGLE * FROM trdir WHERE name = tstc-pgmna.
      IF trdir-subc EQ 'F'.             "Function Group"
        SELECT SINGLE * FROM tfdir   WHERE pname    = tstc-pgmna.
        SELECT SINGLE * FROM enlfdir WHERE funcname = tfdir-funcname.
        SELECT SINGLE * FROM tadir   WHERE pgmid    = 'R3TR'
        AND object   = 'FUGR'
        AND obj_name = enlfdir-area.
        MOVE: tadir-devclass TO v_devclass.
      ENDIF.
    ENDIF.
*   Find SAP Modifactions:
    SELECT * FROM tadir INTO TABLE jtab 
     WHERE pgmid    = 'R3TR'
*     AND object   = 'SMOD'
      AND object   = object
      AND devclass = v_devclass.
    SELECT SINGLE * FROM tstct WHERE sprsl EQ sy-langu AND tcode EQ p_tcode.

    FORMAT COLOR COL_POSITIVE INTENSIFIED OFF.
    WRITE: /(19)  'Transaction Code - ', 20(20) p_tcode, 45(50) tstct-ttext.
    SKIP.
    IF NOT jtab[] IS INITIAL.                               "IF 2"
      WRITE: /(95) sy-uline.
      FORMAT COLOR COL_HEADING INTENSIFIED ON.
*     Exit:
      IF exit = 'X'.
        WRITE: /1   'Exit Name', 44 sy-vline , 'Description'.
*     BADI:
      ELSE.
        WRITE: /1   'BADI Name', 44 sy-vline , 'Description'.
      ENDIF.
      WRITE:/(95) sy-uline.
      LOOP AT jtab.
*       EXIT:
        IF exit = 'X'.
          SELECT SINGLE * FROM modsapt WHERE sprsl = sy-langu
          AND name  = jtab-obj_name.
          FORMAT COLOR COL_NORMAL INTENSIFIED OFF.
          WRITE: /1  space, jtab-obj_name HOTSPOT ON, sy-vline , modsapt-modtext.
*       BADI:
        ELSE.
          SELECT SINGLE * FROM sxs_attrt WHERE sprsl = sy-langu
          AND exit_name = jtab-obj_name.
          FORMAT COLOR COL_NORMAL INTENSIFIED OFF.
          WRITE: /1  space, jtab-obj_name HOTSPOT ON, sy-vline , sxs_attrt-text.
        ENDIF.
      ENDLOOP.
      WRITE: /(95) sy-uline.
      DESCRIBE TABLE jtab.
      SKIP.
      FORMAT COLOR COL_TOTAL INTENSIFIED ON.
      IF exit = 'X'.
        WRITE: / 'No of Exits:', sy-tfill.
      ELSE.
        WRITE: / 'No of BADIs:', sy-tfill.
      ENDIF.
    ELSE.                                                   "IF 2"
      FORMAT COLOR COL_NEGATIVE INTENSIFIED ON.
      WRITE: /(95) 'No User Exit exists'.
    ENDIF.                                                  "IF 2"
  ELSE.                                                     "IF 1"
    FORMAT COLOR COL_NEGATIVE INTENSIFIED ON.
    WRITE: /(95) 'Transaction Code Does Not Exist'.
  ENDIF.                                                    "IF 1"
* Take the user to SMOD for the Exit that was selected:
AT LINE-SELECTION.
  GET CURSOR FIELD field1.
  CHECK field1(4) EQ 'JTAB'.
* For exit:
  IF exit = 'X'.
    SET PARAMETER ID 'MON' FIELD sy-lisel+2(10).
    CALL TRANSACTION 'SMOD' AND SKIP FIRST SCREEN.
* For BADI:
  ELSE.
    CLEAR: bdcdata_wa, bdcdata_tab[].
    bdc_program 'SAPLSEXO' '0100' 'X'.
    bdc_detail 'BDC_CURSOR' 'G_IS_BADI'.
    bdc_detail 'BDC_OKCODE' '=ISSPOT'.
    bdc_detail 'G_IS_BADI' 'X'.
    bdc_program 'SAPLSEXO' '0100' 'X'.
    bdc_detail 'BDC_CURSOR' 'G_BADINAME'.
    bdc_detail 'BDC_OKCODE' '=SHOW'.
    bdc_detail 'G_BADINAME' sy-lisel+2(20).
    opt-dismode = 'E'.
    opt-defsize = 'X'.
    CALL TRANSACTION 'SE18' USING bdcdata_tab OPTIONS FROM opt.
  ENDIF.
```


​     

