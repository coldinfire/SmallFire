---
title: " SAP 发送 Email "
date: 2019-04-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

SAP 的邮件功能可以使用系统标准的邮件工作台收发邮件，也可以调用邮件 API 接口函数进行收发邮件。

## 标准邮件工作台收发邮件

### RZ10：设置 Profile 参数

1、运行事务码 RZ10

![RZ10](/images/ABAP/EMAIL_RZ10_1.png)

2、进入修改界面，点击图中的 Parameter 按钮新建参数

![RZ10](/images/ABAP/EMAIL_RZ10_2.png)

3、新建参数 *icm/server_port_1* 赋值为  *PROT=SMTP,PORT=25000,TIMEOUT=90,PROCTIMEOUT=3600*

![RZ10](/images/ABAP/EMAIL_RZ10_3.png)

默认情况下系统已经有一个参数文件 *icm/server_port_0 = PORT=HTTP,PORT=XXXX* (每个服务可能不一样)，那么这里的 *PORT = #* 就要根据你的参数文件的具体情况，如果已经有了 0，这里你就需要设成 port = 1, 以此类推，PORT 一般设置成 25；这里还有一个选项是timeout 可以设定等待邮件服务器回复时间的最大值。

4、返回，并点击图中的 Parameter 按钮定义虚拟邮件主机

参数格式：`is/SMTP/virt_host_<#> = host:port,port...;`

新建参数 *is/SMTP/virt_host_0* 赋值为 **:25000;* 。；定义虚拟邮件主机主要用来接收邮件的。要注意，参数值最后是有一个分号的。完成后保存，激活。 配置完成后需要重启服务，参数才能生效。

![RZ10](/images/ABAP/EMAIL_RZ10_4.png)

### SU01：SAP 发送邮件用户的维护

1、对于每一个 client，需要创建一个用户作为邮件的接收者，创建了用户，用户类型设置为 Service

![SU01](/images/ABAP/EMAIL_SU01_2.png)

2、给该用户的 Profile 标签添加 S_A.SCON

![SU01](/images/ABAP/EMAIL_SU01_1.png)

3、配置 Address：Email 信息

![SU01](/images/ABAP/EMAIL_SU01_3.png)

### SICF：配置服务

1、执行事务码 SICF，在 Hierarchy Type 中输入 service 执行

![SICF](/images/ABAP/EMAIL_SICF_1.png)

2、执行结果中，双击 SAPconnect

![SICF](/images/ABAP/EMAIL_SICF_2.png)
3、双击后显示 Host data 主机数据。对于 Profile Parameter Number，输入 is/STMP/virt_host_# 中的 # 的值，一般可能是 0，如果不存在的话，下面会有提示的。

![SICF](/images/ABAP/EMAIL_SICF_3.png)

4、登陆数据配置

- 客户端：当前客户端编号
- 用户：前面 SU01 中创建的 service 类型用户
- 语言：EN

![SICF](/images/ABAP/EMAIL_SICF_4.png)

5、Handler List，输入 CL_SMTP_EXT_SAPCONNECT

![SICF](/images/ABAP/EMAIL_SICF_5.png)

6、完成以上配置后，返回保存激活

### SCOT：SAPconnect 配置

![SCOT](/images/ABAP/EMAIL_SCOT_1.png)

1、菜单栏 ：Settings—>Default domain，此处填写邮件服务器。
这个邮件的默认域名比如 sap.com，那么如果在你发送邮件的时候收件人地址如果只写 test 的话，系统会自动加上 @sap.com，如果收件人地址是全的话，这个 domain 不维护关系也不大。

2、维护 SMTP 节点：View—>Note 

Nodes—>Display 或者双击上图 SMTP 后弹出对话框，维护以下信息勾上 "Node in user"。

在 MAIL HOST 和 MAIL PORT 里面，指定发送邮件服务器的地址，比如如果是 163 的话，就应该是 smtp.163.com。

这里 MAIL HOST 填写公司邮件服务器地址，MAIL PORT 填写 25000 选中 Internet 的 Set，弹出新对话框，指定接收地址的地址区域，一般用 * 表示所有邮件都用 SMTP 来发送，其他信息用默认。

这里 SAPconnect 的信息可以有两种方式显示的，如果双击 SMTP 节点弹出的是 JOB 的信息的话，选择菜单中 SYSTEM STATUS，切换到为另一种显示方式即可。

![SCOT](/images/ABAP/EMAIL_SCOT_2.png)

![SCOT](/images/ABAP/EMAIL_SCOT_3.png)

3、**Send job**，选择菜单中的 View—>Jobs，可以检查是否已经有 Jobs 被调度了。选择Jobs—>Create，并指定 Job 名称，点执行按钮，选择SAP&CONNECTALL 变式，并选择 Schedule Job。

选择 Schedule periodically 定期计划，指定时间间隔比如10分钟，选择创建。到这里，基本配置成功了。

4、可以在事物码 **SBWP** 中，选择发送邮件，来测试配置是否成功，输入收件人的邮件地址，点击发送。如果配置成功的话，收一下邮件，应该收到了来自登陆 SAP GUI 的账号中配置的邮件地址的邮件了。

#### SOST：查看发送情况

#### Tips：如果只是使用 SAP 发送而不接收外部回复回来的邮件，那么只需要配置步骤二、四.
