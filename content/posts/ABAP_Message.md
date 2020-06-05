---
title: "ABAP Message 处理"
date: 2018-05-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### MESSAGE ：SE91

 **消息类的操作**

​	使用T-CODE:SE91对Message定义，还能够对Message进行创建，修改及删除等维护操作。Message Short Text字段为类描述，可以定义输入参数&，通过‘&’定义多个占位符,如"1&2&3&"表示有三个输入参数。

![定义消息类](/images/ABAP/SE91.jpg)

MESSAGE E001(ZTEST).

​	E:消息显示类型 (Message共分以下几种类型：E:错误、W:警告、I：信息、A：异常中止、S:成功)

​	001:自定义的消息字段

​	ZTEST:自定义的消息类

**获取标准错误信息**

```javascript
DATA msgtext TYPE string.
CALL BAPI .... 
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
          INTO msgtext
          WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF. 
```

**MESSAGE显示:**

```JS
EX: Message W001(ZTEST) WITH 'P1' 'P2' 'P3'.
	1. 消息ID MESSAGE e001(00) WITH '12345678'. //利用定义的参数
	2. MESSAGE 'XXXXXXXXXX' TYPE 'X'.          //直接附加消息
	3. MESSAGE s001(00) WITH 'No data' DISPLAY LIKE 'E'.
   	   EXIT.                                   //Screen 界面查询数据无，则返回原界面
```

**获取消息内容的BAPI:**

```JS
DATA l_msgid     TYPE bapiret2-id.
DATA l_msgnr     TYPE bapiret2-number.
DATA l_message   TYPE bapiret2-message.
CALL FUNCTION 'BAPI_MESSAGE_GETDETAIL'
        EXPORTING
          id                 = l_msgid
          number             = l_msgnr
          language           = sy-langu
          textformat         = 'HTM'
*         LINKPATTERN        =
*         MESSAGE_V1         =
*         MESSAGE_V2         =
*         MESSAGE_V3         =
*         MESSAGE_V4         =
*         LANGUAGE_ISO       =
       IMPORTING
         message            = l_message.
*         RETURN             =
*       TABLES
*         TEXT               =    
  
```

**消息存储的内表**：T100/T100C/T100S/T100U/T160M

- T100 这个表包括所有的消息
- T100C 通常包括修改后的消息，即修改默认消息类型后的值存在该表中
- T100S 就是表示可以修改消息类型的表