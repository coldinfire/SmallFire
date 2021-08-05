---
title: "SAP Webservice 使用"
date: 2019-02-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### ABAP 调用外部 Webservice

#### 创建代理类

Step1：SE80 选择 package，点击创建 Enterprise Service / Web service-proxy client

![Create Enterprise Service](/images/ABAP/ABAP_WebService1.png)

Step2：参数填写，没有错误的话，会根据指定的 wsdl 文件，产生代理类，激活后可以使用

- 选择 service consumer

  ![Web service-proxy client](/images/ABAP/ABAP_WebService2.png)

- 点击继续，选择 URL/HTTP Destination；如果文件在本地，选择 Local File

  ![Web service-proxy client](/images/ABAP/ABAP_WebService3.png)

-  选择 URL，输入 wsdl 地址，若为 local host 的，需更改为本机的地址

  ![Web service-proxy client](/images/ABAP/ABAP_WebService4.png)

- 点击继续，输入选择包，前缀

  ![Web service-proxy client](/images/ABAP/ABAP_WebService5.png)

- 点击继续，没有错误的话，点击 Complete

  ![Web service-proxy client](/images/ABAP/ABAP_WebService6.png)

#### 创建逻辑端口(Logical Port)：LPCONFIG 或者 SOAMANAGER

通过 Tcode: LPCONFIG 创建逻辑端口；输入代理类，逻辑端口名称，可以设为默认端口。点击创建后，维护逻辑端口的描述。

![LPCONFIG](/images/ABAP/ABAP_WebService7.png)

一般设置

- 运行环境选择 web 服务基础结构
- 调用参数：勾选 url，文本框里输入 web service 地址
-  操作：为每个具体的方法，在 soap 操作里输入 wsdl 文件中定义的 soapAction 等号后面的内容

应用程序里特定设置

- 全局设置里，勾选消息标记，状态管理；否则在激活的时候会提示‘激活不成功’

#### 编写 webservice 程序

```ABAP
REPORT ztest_webservice.
DATA:obj_certif TYPE REF TO ztcertifco_certificate_request,
     obj_output TYPE ztcertifhello_world_soap_out,
     obj_input  TYPE ztcertifhello_world_soap_in,
     wa_obj_input LIKE prxctrl,
     erro_msg   TYPE string,
     obj_exception TYPE REF TO cx_ai_system_fault.
TRY.
  CREATE OBJECT obj_certif
    EXPORTING
      logical_port_name = 'LP01'.
CATCH cx_ai_system_fault INTO obj_exception .
  CALL METHOD obj_exception->get_text
    RECEIVING
      result = erro_msg.
  WRITE /1 erro_msg.
ENDTRY.
*wa_obj_input-field = 'head world'.
*wa_obj_input-value = '1'.
*APPEND wa_obj_input TO obj_input-controller.
TRY.
  CALL METHOD obj_certif->hello_world
    EXPORTING
      input  = obj_input
    IMPORTING
      output = obj_output.
  WRITE:obj_output-hello_world_result.
CATCH cx_ai_system_fault INTO obj_exception .
  CALL METHOD obj_exception->get_text
    RECEIVING
      result = erro_msg.
  WRITE /1 erro_msg.
ENDTRY.
```

#### 测试程序



### SAP 发布 Webservice 供其他系统调用

a. 服务配置，- sicf

b. 函数创建，- SE37：YRFC_TEST_WEB 

c. 生产 webservice, 函数 -> 设置

d. 进行 IE 发布 SOAMANAGER 

e. 外部语言进行调用测试，