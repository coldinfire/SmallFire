---
title: " 选择屏幕添加Layout选择 "
date: 2021-07-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 屏幕选择 Layout

```ABAP
" 选择屏幕参数 "
PARAMETERS: p_disva TYPE slis_vari MEMORY ID zvari.
" 屏幕事件定义 "
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_disva.
  PERFORM variant_search CHANGING p_disva.
" Search Help "
```

#### Function 方法

```ABAP
FORM variant_search  CHANGING cv_variant.
  DATA: zlv_f4_exit TYPE c,
        zls_variant TYPE disvariant.
  CLEAR:zls_variant.

  zls_variant-report = syst-repid.
  CALL FUNCTION 'REUSE_ALV_VARIANT_F4'
    EXPORTING
      is_variant    = zls_variant
      i_save        = 'A'
    IMPORTING
      e_exit        = zlv_f4_exit
      es_variant    = zls_variant
    EXCEPTIONS
      not_found     = 1
      program_error = 2
      OTHERS        = 3.
  IF syst-subrc EQ 0 AND zlv_f4_exit IS INITIAL.
    cv_variant = zls_variant-variant.
  ENDIF.
ENDFORM.                    " VARIANT_SEARCH
```

#### Class 方法

```ABAP
FORM variant_search  CHANGING cv_variant.
  DATA: ls_layout_key  TYPE salv_s_layout_key,
        ls_layout_info TYPE salv_s_layout_info.

  ls_layout_key-report = sy-repid.
  ls_layout_info = cl_salv_layout_service=>f4_layouts( ls_layout_key ).
  cv_variant = ls_layout_info-layout.
ENDFORM.
```

