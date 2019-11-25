---
title: "ALV控制单元格不可编辑"
date: 2018-08-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---



如果设置了可编辑的字段那么 alv 便会添加相应的编辑按钮。如果不需要这些按钮那么可以按上面说过的方法排除他们。

如果已经把整列设为可编辑，而只想让这个列中的某些单元格不可编辑，可以使用这种方法。

正如前面所述，需要告诉 layout 哪个字段是 style 字段。
`Gs_layout-stylefname = ‘CELLSTYLES’.`

下面是关于这些功能的一段代码：把’SEATSMAX’整列设为可编辑状态，但当 CARRID 为’xy’时除外。如果 connid 是’02’时使‘PLANETYPE’可编辑。

把 style table 添加到显示表，并在 layout structure 中说明 style field。在 field catalog 中把相应的 EDIT 设为‘X’。

```JS
FORM adjust_edittables USING pt_list LIKE gt_list[].
 DATA ls_listrow LIKE LINE OF pt_list.
 DATA ls_stylerow TYPE lvc_s_styl.
 DATA lt_styletab TYPE lvc_t_styl.

 LOOP AT pt_list INTO ls_listrow.
IF ls_listrow-carrid = ‘XY’.
 Ls_stylerow-fieldname = ‘SEATSMAX’.
 Ls_stylerow-style = cl_alv_grid=>mc_style_disabled.
 APPEND ls_stylerow TO lt_styletab.
ENDIF.
IF ls_listrow-connid = ‘02’.
 Ls_stylerow-fieldname = ‘PLANETYPE’
Ls_stylerow-.style = cl_alv_grid=>mc_style_enabled.
APPEND ls_Pstylerow TO lt_styletab.
ENDIF.
INSERT LINES OF lt_styletab INTO ls_listrow-cellstyles.
MODIFY pt_list FROM ls_listrow.
 ENDLOOP.
ENDFORM.

```


