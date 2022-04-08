---
title: " Screen 文件对话框 "
date: 2021-11-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

在 selection screen 中，经常要用到文件相关的对话框，比如选择本地文件上传，将数据从 iternal table 下载到本地等，为了增加程序的友好性，需要用到文件相关的对话框。

### KD_GET_FILENAME_ON_F4

这个函数比较简单，只需要提供一个参数  file_name。

```ABAP
PARAMETERS: p_file TYPE localfile OBLIGATORY.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  PERFORM frm_get_filepath USING p_file.
START-OF-SELECTION.
  WRITE: / p_file.
FORM frm_get_filepath USING p_file TYPE localfile.
  CALL FUNCTION 'KD_GET_FILENAME_ON_F4'
    EXPORTING
      static       = ''
      mask         = ',All Files(*.*),*.*,'
    CHANGING
      file_name    = p_file.
ENDFORM.
```

### WS_FILENAME_GET

该函数的作用也是提供文件对话框，返回文件的完整路径。SAP 对这个函数已经标注为**过时** (obosete）。所以不建议在代码中使用。

```ABAP
FORM frm_get_filepath USING p_file TYPE localfile.
  CALL FUNCTION 'WS_FILENAME_GET'
    EXPORTING
      def_path     = 'default_path'
      mask         = ',All Files(*.*),*.*,'
      mode         = '0' "0 For read"
    IMPORTING
      filename     = p_file
    EXCEPTION
      inv_winsys   = 1
      no_batch     = 2
      selection_cancel = 3
      selection_error  = 4
      OTHERS       = 5.
ENDFORM.
```

### F4_FILENAME

```ABAP
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  CALL FUNCTION 'F4_FILENAME'
    IMPORTING 
      file_name = p_file.
```

### FILE_OPEN_DIALOG

cl_gui_frontend_services 类的静态方法 file_open_dialog 方法提供对话框的方式获取文件的完整路径。这个方法功能比较强大，比如可以修饰文件选择框，指定默认选择路径，设置文件类型过滤，同时打开多个文件，返回用户操作等。RC 是返回值, 如果成功获取文件名，返回值 rc 值为 1。

```ABAP
FORM frm_get_filepath USING p_file TYPE localfile.
  DATA: l_files TYPE filetable,
        l_file  TYPE LINE OF filetable,
        rc LIKE sy-subrc.
  CALL METHOD cl_gui_frontend_services=>file_open_dialog
    EXPORTING
      window_title          = 'File Select'
      initial_directory     = 'default_path'
      default_extension     = '*.xls'
      file_filter           = 'All Files (*.*)|*.*|Excel Files (*.xls)|*.xls'
    CHANGING
      file_table            = l_files
      rc                    = rc
    EXCEPTION
      file_open_dialog_failed   = 1
      cntl_error                = 2
      error_no_gui              = 3
      no_supported_by_gui       = 4
      OTHERS                    = 5.
  IF sy-subrc = 0 AND rc = 1.
    READ TABLE l_files INTO l_file INDEX 1.
    p_file = l_file.
  ENDIF.
ENDFORM.
```

