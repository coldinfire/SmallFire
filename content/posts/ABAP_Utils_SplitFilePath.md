---
title: " 分割文件路径和文件名 "
date: 2022-04-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



从文件完整路径中拆分路径和文件名。

### FUNCTION：SO_SPLIT_FILE_AND_PATH

```ABAP
DATA: lv_file_path TYPE string VALUE 'D:\WORKFILES\ITAB.XLSX'.
DATA: lv_path_only type string,
      lv_filename_only type string.

CALL FUNCTION 'SO_SPLIT_FILE_AND_PATH'
  EXPORTING
    full_name     = lv_file_path
  IMPORTING
    stripped_name = lv_path_only
    file_path     = lv_filename_only
  EXCEPTIONS
    x_error       = 1
    OTHERS        = 2.
IF sy-subrc <> 0.
* Implement suitable error handling here
ENDIF.

lv_path_only    : ITAB.XLSX
lv_filename_only: D:\WORKFILES\
```

