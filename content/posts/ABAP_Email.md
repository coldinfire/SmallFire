---
title: " SAP发送Email "
date: 2019-04-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

##  SAP 发送邮件

邮件功能可以配置参数，使用系统标准的邮件工作台收发邮件，还可以调用邮件API接口函数收发邮件。

### Tcode

#### RZ10：设置Profile参数

1、运行事务码RZ10，显示如图所示界面

![RZ10](/images/ABAP/EMAIL_RZ10_1.png)

2、进入修改界面，如下图所示，点图中的“Parameter”按钮

![RZ10](/images/ABAP/EMAIL_RZ10_2.png)

3、新建参数icm/server_port_1赋值为“PROT=SMTP,PORT=25000,TIMEOUT=90,PROCTIMEOUT=3600”

默认情况下已经有一个参数文件icm/server_port_0 = PORT=HTTP,PORT=XXXX(每个服务可能不一样)，那么这里的<*>就是要根据你的参数文件的具体情况，如果已经有了_0,这里你就需要设成_1, 以此类推，PORT一般设置成25。这里还有一个选项是TIMEOUT可以设定等待邮件服务器回复时间的最大值。

![RZ10](/images/ABAP/EMAIL_RZ10_3.png)

4、返回，并点击图中的“Parameter”按钮

新建参数is/SMTP/virt_host_0 赋值为“ * :25000;”。is/SMTP/virt_host_< * > = < host >:< port >,< port >,...;  定义虚拟邮件主机，主要用来接收邮件的，_<*>的设置同上。要注意，参数值最后是有一个分号的。完成后保存，激活。 配置完需要重启服务，参数才能生效。

![RZ10](/images/ABAP/EMAIL_RZ10_4.png)

#### SU01：SAP发邮件用户的维护

1、对于每一个client，需要创建一个用户作为邮件的接收者，创建了用户，用户类型设置为service

![SU01](/images/ABAP/EMAIL_SU01_2.png)

2、并给该用户赋Profile：S_A.SCON

![SU01](/images/ABAP/EMAIL_SU01_1.png)

3、配置Address：Email信息

![SU01](/images/ABAP/EMAIL_SU01_3.png)

#### SICF：配置服务

1、执行事务码SICF

![SICF](/images/ABAP/EMAIL_SICF_1.png)

2、双击SAPconnect

![SICF](/images/ABAP/EMAIL_SICF_2.png)
3、显示Host data 主机数据,对于 Profile Parameter Number，输入 "is/STMP/virt_host_#"中的#的值，一般可能是0，如果不存在的话，下面会有提示的。

![SICF](/images/ABAP/EMAIL_SICF_3.png)

4、登陆数据（logon data）

- 客户端：当前客户端编号
- 用户：前面su01中创建的service用户
- 语言：EN

![SICF](/images/ABAP/EMAIL_SICF_4.png)

5、Handler List,输入CL_SMTP_EXT_SAPCONNECT

![SICF](/images/ABAP/EMAIL_SICF_5.png)

6、完成以上配置后，返回保存激活

#### SCOT：配置

![SCOT](/images/ABAP/EMAIL_SCOT_1.png)

1、菜单栏 ：Settings----Default domain，此处填写邮件服务器。
这个邮件的默认域名比如sap.com，那么如果在你发送邮件的时候收件人地址如果只写test的话，系统会自动加上@sap.com，如果收件人地址是全的话，这个domain不维护关系也不大。

2、维护SMTP节点：View----Note 

Nodes—Display或者双击上图SMTP后弹出对话框，维护 以下信息勾上 "Node in user"。
在MAIL HOST和MAIL PORT下面，指定发送邮件服务器的地址，比如如果是163的话，就应该是smtp.163.com。
这里MAIL HOST填写公司邮件服务器地址,MAIL PORT填写25000选中Internet的Set，弹出新对话框，指定接收地址的地址区域，一般用*表示所有邮件都用SMTP来发送，其他信息用默认。
这里SAPconnect的信息可以有两种方式显示的，如果双击SMTP节点弹出的是JOB的信息的话，选择菜单中SYSTEM STATUS，切换到为另一种显示方式即可。

![SCOT](/images/ABAP/EMAIL_SCOT_2.png)

![SCOT](/images/ABAP/EMAIL_SCOT_3.png)

3、**Send job**,选择菜单中的 View-->Jobs，可以检查是否已经有Jobs被调度了 选择Jobs->Create，并指定Job名称，点执行按钮，选择SAP&CONNECTALL变式，并选择Schedule Job。

选择 Schedule periodiacally定期计划,指定时间间隔，比如10分钟，选择创建。到这里，基本配置成功了。

4、可以在**SBWP**中，选择发送邮件，来测试配置是否成功，输入收件人的邮件地址，点击发送。如果配置成功的话，收一下邮件，应该收到了来自登陆SAP GUI的账号中配置的邮件地址的邮件了。

#### SOST：查看发送情况

#### Tips:如果只是使用SAP发送而不接收外部回复回来的邮件，那么只需要配置步骤二、四.

### 程序

CLASS

- CL_BCS
- CL_DOCUMENT_BCS

Function Module

- SO_DOCUMENT_SEND_API1
- SO_NEW_DOCUMENT_ATT_SEND_API1
- SO_NEW_DOCUMENT_SEND_API1

### 示例程序

```JS
DATA:l_send_request TYPE REF TO CL_BCS VALUE IS INITIAL.
DATA:l_document TYPE REF TO CL_DOCUMENT_BCS VALUE IS INITIAL,
     i_text TYPE BCSY_TEXT,     " Table for body "
     w_text LIKE LINE OF i_text,
     l_subject TYPE SO_OBJ_DES.
DATA:l_sender TYPE REF TO IF_SENDER_BCS VALUE IS INITIAL,
     l_uname TYPE sy-uname.
DATA:l_recipient TYPE REF TO IF_RECIPIENT_BCS VALUE IS INITIAL,
     i_email TYPE STRING.
TRY.
" 创建发送请求 "
  l_send_request = cl_bcs=>create_persistent( ).
" 设定发送内容 " 
  w_text-line = 'Email body context'.
  APPEND w_text TO i_text.
  CLEAR w_text.
  l_document = cl_document_bcs=>create_document(
                   i_type  = 'RAW'       " Type of document "
                   i_subject = l_subject " Email subject "
                   i_text  = i_text " Email body internal table "
                   I_IMPORTANCE = '1'    " Email Document priority "
                    ).
" 增加发送内容到发送请求 "
  CALL METHOD l_send_request->set_document( l_document ).
" 取得发送者（取得发件人，前提是这个邮箱地址能发邮件，并且不需要密码）"
  l_uname = sy-uname.
  l_sender = cl_sapuser_bcs=>create( l_uname ).
  CALL METHOD l_send_request->set_sender
    EXPORTING
      i_sender = l_sender.
" 设置收件人 "
   LOOP AT it_mailaccept.
     CLEAR i_email.
     CONCATENATE it_mailaccept-name '@XXXXX.COM' INTO i_email.
     TRANSLATE i_email TO LOWER CASE.
     l_recipient = cl_cam_address_bcs=>create_internet_address( i_email ).
     CALL METHOD l_send_request->add_recipient
       EXPORTING
         i_recipient  = l_recipient
         i_express    = 'X'
         i_copy       = ' '
         i_blind_copy = ' '
         i_no_forward = ' '.
   ENDLOOP.
" 设置抄送人 "
    LOOP AT it_mailaccept_copy.
      CLEAR i_email.
      CONCATENATE it_mailaccept_copy-name '@XXXXX' INTO i_email.
      TRANSLATE i_email TO LOWER CASE.
      l_recipient = cl_cam_address_bcs=>create_internet_address( i_email ).
      CALL METHOD l_send_request->add_recipient
        EXPORTING
          i_recipient  = l_recipient
          i_express    = 'X'
          i_copy       = 'X'
          i_blind_copy = ' '
          i_no_forward = ' '.
    ENDLOOP.
" 立即发送 "
  CALL METHOD l_send_request->set_send_immediately( 'X' ).
" 发送 "
  CALL METHOD l_send_request->send( 
    EXPORTING
      I_WITH_ERROR_SCREEN = 'X'
  ).
  COMMIT WORK.
  IF SY-SUBRC = 0.
    WRITE :/ 'Mail sent successfully'.
  ENDIF.
  CATCH cx_document_bcs INTO l_bcs_exception.
  CATCH cx_send_req_bcs INTO l_send_exception.
  CATCH cx_address_bcs  INTO l_addr_exception.
ENDTRY.
```

### 相关链接

- [发送带文本附件的Email](https://coldinfire.github.io/2019/ABAP_EmailText/)
- [发送带Excel附件的Email](https://coldinfire.github.io/2019/ABAP_EmailExcel/)