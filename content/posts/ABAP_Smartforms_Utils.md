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

#### Table和template区别：

- 1、`table` 行为动态，数据输出时会根据列宽自动换行，可以固定列宽，
  但是默认情况下控制不了行高，如果要想 template 一样固定行高，需要将 table 的无换页属性打钩；通过 Main window （还是 table 控件？）的高度来自动翻页.
- 2、`template` 为静态，固定列宽、行高，当输出数据过长时会自动截断，通常被用于静态表单开发。
  template 跟 loop 嵌套使用，可以实现固定行高、列宽的表单开发。需要手动翻页，并程序中计算页码；（程序中计算页码？）.

#### Smartforms Debug

1. 使用TCode:Smartforms进入，输入smartform name或则同个NACE查找到smartform
2. 在smartform中找到需要Debug的一段代码，打上相应的断点
3. 在导航栏中通过 Environment –> Function Module Name 获取Function module
4. 在SE37中打开该Function Module,进入Main program找到Perform GLOBAL_INIT
5. 该FORM中进行所有的程序数据初始化，所有smartform中的程序行在此处定义，可以打断点
6. 或则在Main program中找到想要打断点的代码位置，运行Smartform，会进入断点，进行查错

#### Page Break in a smartforms

根据字段值进行分页，比较该字段值，如果该值被更改，则进行分页。

- 创建一个带有主窗口的页面，将项目详细信息显示为smartform中的Table。
- 在主窗口内创建一个循环，循环内部表数据。
- 在循环中创建一个命令，在命令的条件选项卡中检查字段值。
- 选中复选框（转到新页面），然后提供要显示的下一页。
- 在命令下创建文本以显示数据。
- 根据在命令条件选项卡中给出的条件，分页符将自动被触发，所需的数据将流到下一页。

#### Smartforms 连续打印

调用 smartforms 时直接打印，不出现打印预览窗口

```JS
DATA: fm_name TYPE rs38l_fnam.
DATA: ls_control_param TYPE ssfctrlop .
DATA: ls_composer_param TYPE ssfcompop .
	
  ls_control_param-langu = sy-langu.
  ls_control_param-no_open = 'X'.
  ls_control_param-no_close = 'X'.
  ls_control_param-no_dialog = 'X'.  " Not show dialog

  ls_composer_param-tddest = 'LP01'. " Printer
  ls_composer_param-tdimmed = 'X'.   " Print Immediately (Print Parameters)
  ls_composer_param-tddelete = 'X'.  " Delete After Printing (Print Parameters)
   
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
```

