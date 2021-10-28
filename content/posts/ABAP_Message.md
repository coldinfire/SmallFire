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

**消息存储的内表**：T100/T100C/T100S/T100U/T160M

- T100：该表包括所有的消息
- T100C：通常包括修改后的消息，即修改默认消息类型后的值存在该表中
- T100S：表示可以修改消息类型的表

#### 消息类的操作

使用事物码 SE91 对 Message 定义，还能够对 Message 进行创建，修改及删除等维护操作。

Message Short Text 字段为类描述，可以定义输入参数&，通过‘&’定义多个占位符，如"1&2&3&"表示有三个输入参数。

![定义消息类](/images/ABAP/SE91.jpg)

MESSAGE E001(ZTEST).

- E：消息显示类型 (Message共分以下几种类型：E:错误、W:警告、I:信息、A:异常中止、S:成功)


- 001：自定义的消息字段


- ZTEST：自定义的消息类

#### 系统消息组成完整文本

使用 MESSAGE INOT text：

```ABAP
DATA msgtext TYPE string.
"CALL BAPI"
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    INTO msgtext
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF. 
```

调用函数将消息组成文本：

```ABAP
DATA msgtext TYPE string.
CALL FUNCTION 'MESSAGE_TEXT_BUILD'
  EXPORTING
    msgid = sy-msgid
    msgnr = sy-msgno
    msgv1 = sy-msgv1
    msgv2 = sy-msgv2
    msgv3 = sy-msgv3
    msgv4 = sy-msgv4
  IMPORTING
    message_text_output = msgtext.
```

#### MESSAGE 显示

- `MESSAGE e001(00) WITH '12345678'. `：利用定义的参数
-  `MESSAGE 'XXXXXXXXXX' TYPE 'X'. `：直接附加消息
- `MESSAGE s001(00) WITH 'No data' DISPLAY LIKE 'E'`

#### 增强的 Warning Message 不显示

在 ME22N 的增强面临这样的问题。 使用以下方法显示警告消息并将其附加到在检查文档时获得的日志中的警告消息。

```ABAP
DATA: msgv1 TYPE symsgv,
      msgv2 TYPE symsgv,
      msgv3 TYPE symsgv,
      msgv4 TYPE symsgv.
CALL METHOD cl_message_mm=>create
  EXPORTING
    im_msgid = 'Z01'
    im_msgty = 'W'
    im_msgno = '195'
    im_msgv1 = msgv1
    im_msgv2 = msgv2
    im_msgv3 = msgv3
    im_force_collect = 'X'.
```

#### 用函数方式返回消息显示

- MESSAGES_INITIALIZE：Message init

- MESSAGE_STORE：Store message
- MESSAGES_GIVE：Message show
- MESSAGES_SHOW

```ABAP
DATA: lt_mesg TYPE TABLE OF mesg WITH HEADER LINE.
LOOP AT message.
  lt_mesg-zeile = message-row.
  lt_mesg-msgty = message-type.
  lt_mesg-text = message-message.
  lt_mesg-arbgb = message-id.
  lt_mesg-txtnr = message-number.
  lt_mesg-msgv1 = message-message_v1.
  lt_mesg-msgv2 = message-message_v2.
  lt_mesg-msgv3 = message-message_v3.
  lt_mesg-msgv4 = message-message_v4.
  APPEND lt_mesg.
  CLEAR lt_mesg.
ENDLOOP.
**--messages init
CALL FUNCTION 'MESSAGES_INITIALIZE'
  EXPORTING
    collect_and_send = ''.
**--message store
LOOP AT lt_mesg.
  CALL FUNCTION 'MESSAGE_STORE'
    EXPORTING
      arbgb                  = lt_mesg-arbgb
*     EXCEPTION_IF_NOT_ACTIVE       = 'X'
      msgty                  = lt_mesg-msgty
      msgv1                  = lt_mesg-msgv1
      msgv2                  = lt_mesg-msgv2
      msgv3                  = lt_mesg-msgv3
      msgv4                  = lt_mesg-msgv4
      txtnr                  = lt_mesg-txtnr
      zeile                  = lt_mesg-zeile
*   IMPORTING
*     ACT_SEVERITY           =
*     MAX_SEVERITY           =
    EXCEPTIONS
      message_type_not_valid = 1
      not_active             = 2
      OTHERS                 = 3.
  IF sy-subrc <> 0.
* Implement suitable error handling here
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDLOOP.
**--message show
REFRESH: lt_mesg.
CALL FUNCTION 'MESSAGES_GIVE'
  TABLES
    t_mesg = lt_mesg.
CALL FUNCTION 'MESSAGES_SHOW'
  EXPORTING
    show_linno         = ''
    i_use_grid         = ''
    i_amodal_window    = ''
  EXCEPTIONS
    inconsistent_range = 1
    no_messages        = 2
    OTHERS             = 3.
```

#### 获取消息内容的BAPI

```ABAP
DATA l_msgid    TYPE bapiret2-id.
DATA l_msgnr    TYPE bapiret2-number.
DATA l_message  TYPE bapiret2-message.
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
*         TEXT               =    .
```

