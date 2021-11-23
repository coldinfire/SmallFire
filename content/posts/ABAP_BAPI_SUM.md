---
title: " BAPI 总结列表 "
date: 2018-10-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI

---

### 本文主要记录一些收集来的BAPI

在SE38环境下的程序名输入栏

- 输入 **DEMO** 后按 F4，你可以查到 SAP 所有的 DEMO 示例程序，会学到很多 ABAP 功能的实现方法

- 输入 **BCALV** 后按 F4，你可以查到很多 ALV 示例程序 。

## 函数名 描述 
```JS
• CONVERSION_EXIT_ALPHA_INPUT   数字串前补0
• CONVERSION_EXIT_ALPHA_OUTPUT  消除数字串前的0
• TERMINAL_ID_GET    获得端末id
• TH_USER_INFO       获得当前用户的信息 (会话，登陆的工作台等)
• BKK_GET_MONTH_LASTDAY         根据日期获取当前月的最后一天 
• CONVERSION_EXIT_CUNIT_OUTPUT  单位转换
• SJIS_DBC_TO_SBC    全角转半角
• SJIS_SBC_TO_DBC    半角转换为全角
• CO_R0_CHECK_DECIMAL_POINT     根据单位检查数据的小数位
• CONVERSION_EXIT_MATN1_INPUT   物料号码转换函数
• CONVERSION_EXIT_MATN1_OUTPUT  同上相反
• CONVERT_TO_LOCAL_CURRENCY     按照指定日期汇率转换金额为指定货币类型
• SSF_FUNCTION_MODULE_NAME      根据form名取得对应的函数名(SmartForm) 
• K_WERKS_OF_BUKRS_FIND         返回一个特定公司代码的所有工厂。
• DATE_CHECK_PLAUSIBILITY       日期CHECK
• DATE_GET_WEEK      返回一个日期所在的周数。 
• MONTH_NAMES_GET    获得所有的月和名字
• RP_CALC_DATE_IN_INTERVAL      年月日加减
• SD_DATETIME_DIFFERENCE        两日期作差 
• RP_LAST_DAY_OF_MONTHS         获得一个月的最后一天
• DATE_CHECK_PLAUSIBILITY       检查一个日期是否是SAP的有效格式
• HOLIDAY_GET        基于Factory Calendar&/ Holiday Calendar提供了一个节日表。
• SAPGUI_PROGRESS_INDICATOR	    显示一个进度条 
• CLOI_PUT_SIGN_IN_FRONT        将负号前置， SAP默认将负号放在数字后面
• TH_USER_LIST       显示登陆到应用服务器的用户列表
• UNIT_CONVERSION_SIMPLE        衡量单位转换
• SXPG_COMMAND_CHECK            检查用户是否有执行某个命令的权限
• SXPG_COMMAND_EXECUTE          检查用户是否有执行某个命令的权限，拥有授权则执行命令
• FILENAME_GET       弹出一个文件选择对话框
	DATA out(60) TYPE c.
	 CALL FUNCTION 'FILENAME_GET'  
	 EXPORTING    
		filename = 'c:/1.txt'    
		title = 'GET FILENAME'  
	  IMPORTING   
		filename = OUT.
• GET_JOB_RUNTIME_INFO          获得job相关信息
• BP_JOBLOG_READ                获得job log的执行结果
• DATE_CONVERT_TO_FACTORYDATE   把输入日期转为工厂日历日期
• VIEW_MAINTENANCE_CALL         维护表视图 
```
## 函数名 描述
```JS
• BAPI_TRANSACTION_COMMIT   COMMIT WORK AND WAIT.
• BAPI_TRANSACTION_ROLLBACK ROLLBACK WORK.
• SD_VBAP_READ_WITH_VBELN   根据销售订单读取表vbap中的信息
• EDIT_LINES                把READ_TEXT返回的LINES中的行按照TDFORMAT=“*”重新组织
• DY_GET_FOCUS              获得屏幕焦点 
• DY_GET_SET_FIELD_VALUE    获得或者设置屏幕字段的值 
• F4IF_INT_TABLE_VALUE_REQUEST 显示检索help 
• READ_TEXT            读取长文本
• INIT_TEXT           上传长文本到SAP。
• POSTAL_CODE_CHECK   检查邮政编码 
• MESSAGE_TEXT_BUILD  把消息转为文本 
• POPUP_TO_CONFIRM    弹出确认窗口 
```
## 函数名 描述 
```JS
• cl_gui_frontend_services=>gui_upload     上传到服务器
• cl_gui_frontend_services=>gui_download   下载到服本地
• SSF_FUNCTION_MODULE_NAME SMARTFORMS      输出报表时，生成一个函数名称，然后CALL这个名称 
• POPUP_TO_DECIDE_LIST      弹出供选择窗口
• ABAP_DOCU_DOWNLOAD  	    以HTML格式下载ABAP文档
• ARFC_GET_TID 		    以十六进制形式返回终端的IP地址
• BAL_* 		    容纳了SAP的应用程序日志所有的函数模块
• BP_EVENT_RAISE 	    在ABAP/4 程序中触发一个事件
• CLPB_EXPORT 		    从内表导入到剪贴板
• CLPB_IMPORT 		    从剪贴板导入内表
• COMMIT_TEXT 		    To load long text into SAP 
• CONVERT_OTF               将SAP文档(SAP Script)转换成其他类型
  example:CALL FUNCTION 'CONVERT_OTF'
	EXPORTING
	  FORMAT = 'PDF'
	IMPORTING
	  BIN_FILESIZE = FILE_LEN
	TABLES
	  OTF = OTFDATA
	  LINES = PDFDATA
	EXCEPTIONS
	  ERR_MAX_LINEWIDTH = 1
	  ERR_FORMAT = 2
	  ERR_CONV_NOT_POSSIBLE = 3
	  OTHERS = 4.
```
## 函数名 描述 
```JS
• DYNP_VALUES_READ 	           读取SCREEN字段的值，也可以用来读取报表SELECTION SCREEN
• DYNP_VALUES_UPDATE		  更新屏幕字段的值 
• ENQUE_SLEEP 				   在继续处理之前等待一个指定的时间
• ENQUEUE_ESFUNCTION 	      	锁定一个ABAP程序使它不可以被执行 注意不要用SY-REPID来传递你的报表名
• EPS_GET_FILE_ATTRIBUTES 		获得文件属性
• EPS_GET_DIRECTORY_LISTING 	返回一个本地或网络目录的文件列表
• F4_DATE 				弹出一个窗口显示一个日历允许用户选择一个日期
• F4IF_SHLP_EXIT_EXAMPLE 		F4接口模块
• FTP_CONNECT    	     打开并登陆FTP服务器的连接
• FTP_COMMAND    	     在FTP服务器上执行一个命令
• FTP_DISCONNECT 	     关闭指向FTP服务器的连接
• FORMAT_MESSAGE 	     获取消息ID和数字，并将其放入变量
• GET_GLOBAL_SYMBOLS     返回一个程序的tables, select options, texts, etc.包含selection screen的文本
• GET_INCLUDETAB 	     获得一个程序的INCLUDES列表
• GUI_CREATE_DIRECTORY	     在显示服务器端创建一个目录
• GUI_DELETE_FILE	     在显示服务器端删除一个文件 
• GUI_DOWNLOAD		     从应用服务器下载内表到显示服务器
• GUI_EXEC		     调用一个文件或程序，取代了WS_EXECUTE
• GUI_GET_DESKTOP_INFO	     获得客户端桌面信息，取代了WS_QUERY 
• GUI_REMOVE_DIRECTORY	     从显示服务器删除一个目录 
• GUI_RUN		     启动一个文件或程序 
• GUI_UPLOAD		     从显示服务器上传文件到应用服务器，取代了WS_UPLOAD
• HELP_START		     为一个字段显示帮助
• LIST_TO_ASCII 	    将ABAP报表从 OTF形式转换成ASCII 形式。
• LIST_FROM_MEMORY	    将内容列表输出到内存,参照WRITE_LIST。
• MS_EXCEL_OLE_STANDARD_OLE      创建一个文件并自动启动Excel 。
• CONVERT_OTFSPOOLJOB_2_PDF      将OTF假脱机转换为PDF(Sap脚本文档)
• CONVERT_ABAPSPOOLJOB_2_PDF     将ABAP假脱机输出转换为PDF
• POPUP_TO_CONFIRM_LOSS_OF_DATA  弹出一个对话框告知用户有可能丢失数据，询问是否操作继续
• POPUP_TO_CONFIRM_STEP          弹出一个对话框询问用户是否操作继续。 
• POPUP_TO_CONFIRM_WITH_MESSAGE  可以显示定制的提示信息的确认窗口 类似POPUP_TO_CONFIRM_STEP只是多三行的
								 文本错误诊断提示。
• POPUP_TO_CONFIRM_WITH_VALUE    用此函数可以建立一个对话框用于询问用户是否执行某步操作，该操作可能
	会丢失数据，用户可以选择Yes No或者Cancel。该函数可以传入一个标题,两行的文本（提示问题）和一个对象值 
• POPUP_TO_DECIDE     		   显示一个对话框，用户可以两个操作中的一个或者取消。可以传入三行提示文本
• POPUP_TO_DECIDE_WITH_MESSAGE     类似POPUP_TO_DECIDEPOPUP_TO_DISPLAY_TEXT  显示多行信息的窗口
• POPUP_TO_SELECT_MONTH   	   弹出一个对话框供选择月。
• POPUP_WITH_TABLE_DISPLAY 	   提供一个表的显示供用户选择一个，选中时返回的表行的值
• PRICING 		获得定价条件
• PROFILE_GET 	从INI文件读取一条记录
• PROFILE_SET 	往INI文件写一条记录
• REGISTRY_GET    从注册表读取一条记录
• REGISTRY_SET    在注册表里设置一条记录
• RFC_ABAP_INSTALL_AND_RUN  	   当MODE参数值为‘F’时运行PROGRAM表中的序
• RH_GET_ACTIVE_WF_PLVAR 	   获得激活的HR计划
• RH_START_EXCEL_WITH_DATA	   启动Excel并用内表给文件赋值RH_STRUC_GET –返回所有相关的组织信息
• RPY_DYNPRO_READ		   读取屏幕
• RPY_TRANSACTION_READ 		   给定一个事务代码，获得其程序和屏幕；或给定一个程序和屏幕获得事务代码
• RS_COVERPAGE_SELECTIONS	   获得一个报表的选择参数列表
• RS_REFRESH_FROM_SELECTOPTIONS    获得当前选择屏幕的内容
• RS_SEND_MAIL_FOR_SPOOLLIST	   在程序中给SAP office 发送消息
• RS_VARIANT_CONTENTS		   获得一个变式的内容
• RZL_SLEEP			   将当前程序挂起
• RZL_SUBMIT			   提交一个远程报表
• RZL_READ_DIR_LOCAL		   读取应用服务器的目录
• RZL_READ_DIR			   如果服务器名字左部为空，从本地读取目录，否则读取远程服务器的目录
• RZL_READ_FILE			   如果为给定服务器名字则读取本地文件，否则读取远程服务器文件
• RZL_WRITE_FILE_LOCAL 		   将内表保存到显示服务器(not PC). 不使用OPEN DATASET因此避免了授权检查。
• SCROLLING_IN_TABLE		   当编写模块池的时候可以用它来处理滚动
• SO_NEW_DOCUMENT_ATT_SEND_API1    将文档作为邮件的一部分发送
• SO_SPLIT_FILE_AND_PATH	   将一个包含路径的全文件名分割为文件名和路径
• SO_SPOOL_READ			   根据SPOOL号获得printer spool
• SO_WIND_SPOOL_LIST		   根据用户浏览printer spool号
• SX_OBJECT_CONVERT_OTF_PDF 	   从OTF转换为PDF (SAP 脚本转换)
• SX_OBJECT_CONVERT_OTF_PRT 	   从OTF转换为打印机格式(SAP 脚本转换)
• SX_OBJECT_CONVERT_OTF_RAW 	   从OTF转换为ASCII(SAP 脚本转换)
• SXPG_CALL_SYSTEM		   检查用户是否有执行某个命令的权限
• SXPG_COMMAND_LIST_GET		   获得一个包含所有定义的外部OS命令的列表
• SXPG_COMMAND_DEFINITION_GET      从R/3系统数据库读取单个外部OS命令的定义
• TH_DELETE_USER		 	剔除一个用户，效果同SM04
• TH_ENVIRONMENT			获得UNIX环境
• TH_POPUP				在特定用户屏幕上显示一个系统消息
• TH_REMOTE_TRANSACTION		 在远程服务器上运行事务代码
• UPLOAD 		上传文件到显示服务器
• UPLOAD_FILES 	        传一个或多个文件
• WRITE_LIST		显示一个列表对象
• WS_DOWNLOAD	        将内表下载到显示服务器
• WS_EXCEL		启动EXCELWS_EXECUTE –执行一个程序
• WS_FILE_DELETE	删除一个文件
• WS_FILENAME_GET       调用文件选择对话框
• WS_MSG		显示一个对话框显示在线消息
• WS_UPLOAD		从显示服务器上传文件到内表
• WS_VOLUME_GET 	获得终端设备标签
• WWW_LIST_TO_HTML      运行一个报表之后，调用这个方法将列表输出转换成HTML
• DATE_CONVERT_TO_FACTORYDATE	  把输入日期转为工厂日历日期
• MESSAGE_TEXT_BUILD 	          把消息转为文本
• DAY_IN_WEEK			  用来得到将来/过去的日期的
• RP_CALC_DATE_IN_INTERVAL	  日期的加减
• BKK_ADD_MONTH_TO_DATE		  一组有用的用户交互窗口函数
• POPUP_TO_CONFIRM_LOSS_OF_DATA   显示有YES/NO的弹出窗口，提示用户未保存的数据将丢失 
• POPUP_TO_CONFIRM_STEP		  提示是否确认操作的弹出窗口 
• POPUP_TO_CONFIRM_WITH_MESSAGE   可以显示定制的提示信息的确认窗口
• POPUP_TO_CONFIRM_WITH_VALUE	  显示确认用户对某个特定对象的操作的弹出窗口
• POPUP_TO_DECIDE 		  将待确认选项以单选按钮的方式显示的弹出窗口 
• POPUP_TO_DECIDE_WITH_MESSAGE    带消息的确认窗口 
• POPUP_TO_DISPLAY_TEXT           显示多行信息的窗口 
• POPUP_TO_SELECT_MONTH		  月份选择窗口 
• POPUP_WITH_TABLE_DISPLAY	  有表格对象的确认窗口 一组操纵客户端文件系统的函数
• GUI_CREATE_DIRECTORY	   	  在PC上建立文件目录
• GUI_DELETE_FILE 			   删除PC上的文件 
• GUI_DOWNLOAD 				  文件下载函数 
• GUI_EXEC 				执行PC上的程序，或者打开文件 
• GUI_GET_DESKTOP_INFO 	得到PC客户端的系统信息，比如操作系统等 
• GUI_REMOVE_DIRECTORY 	删除PC目录 
• GUI_RUN	 			 运行PC程序（ShellExecute） 
• GUI_UPLOAD 			  从PC上传程序 判断某天是否是假日 
• HOLIDAY_CHECK_AND_GET_INFO
• ABAP_DOCU_DOWNLOAD	   将ABAP文档以HTML格式下载
• GET_CURRENT_YEAR		 得到当前的财政年（fiscal year） 察看某日期的属性，包括该日期是星期几，第几天
						（周• 2=2），是不是公共假期等，需要输入国家日历。
• DAY_ATTRIBUTES_GET 	  返回有关一天的有用信息。 会告诉你星期几作为一个单词，星期几，是否是假日等等。
• REUSE_ALV_VARIANT_DEFAULT_GET 	读取默认的布局 
• REUSE_ALV_VARIANT_EXISTENCE 	  检测指定布局是否存在 
• REUSE_ALV_VARIANT_F4 			 显示布局格式选择对话窗. 具体可参考BALVST02_GRID程序。
```
