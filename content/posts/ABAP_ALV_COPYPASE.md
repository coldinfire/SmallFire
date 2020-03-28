---
title: "ALV复制内容到剪贴板"
date: 2020-03-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - abaputils

---

引用链接：[ALV复制内容到剪贴板](https://mp.weixin.qq.com/s/h9vRrzQUir8epaypNdcC6w)



用在ALV的USER_COMMAND里面
复制ALV内容到剪贴板，已经考虑了ALV的列隐藏、筛选，负号已经提前
如果有选中的列，则复制选中的列，如果没有选中的列，复制所有可见的列 

如果要保存ALV的数据到Excel，可以先复制，然后到Excel粘贴。

```JS
FORM user_command USING r_ucomm LIKE sy-ucomm
                        rs_selfield TYPE slis_selfield.
  CASE r_ucomm.
    WHEN 'TOCLIP'.
      PERFORM itabtoclip_alv TABLES itab_dis.
  ENDCASE.
ENDFORM. 
```



```JS
*&--------------------------------------
*&  用在ALV的USER_COMMAND里面
*&  复制ALV内容到剪贴板，已经考虑了ALV的列隐藏、筛选，负号已经提前
*&  如果有选中的列，则复制选中的列，如果没有选中的列，复制所有可见的列 
*&      Form  itabtoclip_alv
*---------------------*
FORM itabtoclip_alv TABLES itab.
  DATA: fldcat TYPE slis_t_fieldcat_alv WITH HEADER LINE,
        marked TYPE slis_t_fieldcat_alv WITH HEADER LINE,
        entries TYPE slis_t_fieldcat_entries WITH HEADER LINE.

  DATA: charc TYPE char256,
        ftype.

  DATA: htab TYPE c VALUE cl_abap_char_utilities=>horizontal_tab.
  DATA: lt_clip TYPE TABLE OF char2048 WITH HEADER LINE.
  FIELD-SYMBOLS <fs_fld>.

  CALL FUNCTION 'REUSE_ALV_GRID_LAYOUT_INFO_GET'
    IMPORTING
      et_fieldcat         = fldcat[]
      et_marked_columns   = marked[]
      et_filtered_entries = entries[]
    EXCEPTIONS
      no_infos            = 1
      program_error       = 2
      OTHERS              = 3.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
         WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
  IF marked[] IS INITIAL.
    marked[] = fldcat[].
    DELETE marked WHERE no_out = 'X'.
  ENDIF.
  SORT entries.
  CHECK marked[] IS NOT INITIAL.
  CHECK itab[] IS NOT INITIAL.

  LOOP AT itab.
    READ TABLE entries WITH KEY table_line = sy-tabix BINARY SEARCH.
    CHECK sy-subrc <> 0.
    CLEAR lt_clip.
    LOOP AT marked.
      ASSIGN COMPONENT marked-filename OF STRUCTURE itab TO <fs_fld>.
      CHECK sy-subrc = 0.

      DESCRIBE FIELD <fs_fld> TYPE ftype.
      CASE  ftype.
        WHEN  'I' OR 'P' OR 'F' OR 'a' OR 'e'.
          charc = ABS( <fs_fld> ).
          CONDENSE  charc NO-GAPS.
          IF <fs_fld> < 0.
            CONCATENATE '-' charc INTO charc.
          ENDIF.
        WHEN OTHERS.
          WRITE <fs_fld> TO charc.
      ENDCASE.
      CONCATENATE lt_clip htab INTO lt_clip.
    ENDLOOP.
    SHIFT lt_clip.
    APPEND lt_clip.
  ENDLOOP.

  CHECK lt_clip[] IS NOT INITIAL.
  CALL FUNCTION 'CLPB_EXPORT'
    TABLES
      data_tab   = lt_clip
    EXCEPTIONS
      clpb_error = 1
      OTHERS     = 2.
  IF sy-subrc <> 0.
    MESSAGE s000(00) WITH '已经导出到剪贴板'.
  ELSE.
    MESSAGE e000(00) WITH '导出到剪切板错误'.
  ENDIF.
ENDFORM.                    "itabtoclip_alv
```

