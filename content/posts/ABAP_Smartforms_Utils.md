---
title: "Smartforms常用工具"
date: 2018-07-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 中文乱码问题

如果预览时中文正常，打印出来是乱码的情况，可以尝试在中文环境下重新维护并激活。再次打印应该可以解决。如果还有问题，则检查语言包是否支持中文(或则其他需要使用的语言)。

### Table和Template区别：

- 1、`Table` 行为动态，数据输出时会根据列宽自动换行，可以固定列宽，
  但是默认情况下控制不了行高，如果要想 template 一样固定行高，需要将 table 的无换页属性打钩；通过 Main window （还是 table 控件）的高度来自动翻页.
- 2、`Template` 为静态，固定列宽、行高，当输出数据过长时会自动截断，通常被用于静态表单开发。
  template 跟 loop 嵌套使用，可以实现固定行高、列宽的表单开发。需要手动翻页，并程序中计算页码；（或则在程序中设计好固定行数+计算页码）.

### Smartforms Debug

1. 使用TCode:Smartforms进入，输入smartform name或则同个NACE查找到smartform
2. 在smartform中找到需要Debug的一段代码，打上相应的断点
3. 在导航栏中通过 Environment –> Function Module Name 获取Function module
4. 在SE37中打开该Function Module,进入Main program找到Perform GLOBAL_INIT
5. 该FORM中进行所有的程序数据初始化，所有smartform中的程序行在此处定义，可以打断点
6. 或则在Main program中找到想要打断点的代码位置，运行Smartform，会进入断点，进行查错

### 输出格式设置

所有数字变量后面一般都要加 (C)，数字靠右，文字靠左

| Syntax     | Desc         | Syntax      | Desc                            |
| ---------- | ------------ | ----------- | ------------------------------- |
| &field(n)& | 只显示前n位  | &field(.n)& | 显示n为小数位                   |
| &field(S)& | 忽略正负号   | &field(T)&  | 忽略千分位分隔符                |
| &field(<)& | 符号在左边   | &field(L)&  | 将日期转换为本地格式显示        |
| &field(>)& | 符号在右边   | &field(I)&  | 如果字段INITIAL，不输出         |
| &field(Z)& | 不输出前导零 | &field(nR)& | n位显示，居右(必须设置长度才可) |
| &field(C)& | 空格压缩     | &field(Z)&  |                                 |

### 页码混乱问题

使用 SFSY-FORMPAGES 显示总页数的时候，如果页数大于 9,，将会在前 10 页显示成星号。解决办法：可以添加 3ZC，&SFSY-PAGE(3ZC)&/&SFSY-FORMPAGES(3ZC)&，不过可能会出现字体颠倒或者重叠的现象，用一个单独的窗口来存放显示页码的文本，并且把窗口的类型设置为 L (最终窗口) 就 OK 了。

### 金额数字显示问题

如果金额或者数量字段显示不出来的话，可以在 “货币 / 数量字段” 标签中指定相应的数据类型。

### Smartforms 设置参数实现连续打印

调用 smartforms 时直接调用打印机打印，不出现打印预览窗口

```JS
DATA: fm_name TYPE rs38l_fnam.
DATA: ls_control_param TYPE ssfctrlop .
DATA: ls_composer_param TYPE ssfcompop .
	
ls_control_param-langu = sy-langu.
ls_control_param-no_open = 'X'.
ls_control_param-no_close = 'X'.
ls_control_param-no_dialog = 'X'.  " Not show dialog "
ls_composer_param-tddest = 'LP01'. " Printer "
ls_composer_param-tdimmed = 'X'.   " Print Immediately (Print Parameters) "
ls_composer_param-tddelete = 'X'.  " Delete After Printing (Print Parameters) "   
lw_composer_param-tdcopies = 2.    " 重复打印次数"
lw_composer_param-tdpageslct = '1-3'. "控制打印的页码，1,2/1-3"
* 根据 SmartForm 名称获得 Form 的 Function Name
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname = 'FORM_NAME'
  IMPORTING
     fm_name = fm_name
  EXCEPTIONS
     no_form = 1
     no_function_module = 2
    OTHERS = 3 .
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
* 调用Function 实现打印
CALL FUNCTION fm_name
  EXPORTING
    control_parameters = ls_control_param
    output_options     = ls_composer_param
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
```

### 打印次数的记录

```htnml
DATA: ls_control_param TYPE ssfctrlop .
DATA: ls_composer_param TYPE ssfcompop .
DATA: ls_output_info TYPE ssfcrescl.

ls_composer_param-TDIEXIT = 'X'. " 预览打印后直接退出 "
CALL FUNCTION fm_name
  EXPORTING
    control_parameters = ls_control_param
    output_options     = ls_composer_param
  IMPORTING
    job_output_info    = ls_output_info
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
If sy-subrc = 0.  
  if ls_output_info-OUTPUTDONE = 'X'. “ 是否输出到打印机 "  
    " 增加打印次数，并把它写到相应的自定义表中 " 
  endif.  
ENDIF. 
```

