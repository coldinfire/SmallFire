---
title: "Smartforms Barcode"
date: 2018-08-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



### Smartforms 打印条码

**传统的：将数据发送到打印机上，由打印机将数据转换成 条码图案，然后进行打印。**

在 SAP Smartforms 里实现条码打印。在客户机里安装 [www.tec-it.com](http://www.tec-it.com/) 里的插件

- 1.定义输出设备，将输出设备分配到设备类型里，T-CODE:SPAD
  - sap 下安装打印机：用 SPAD 来安装打印机，在配置里点输出设备，在输出设备里点创建，输入输出设备名称，如 LP01
    在设备属性的设备类型里选择 CNSAPWIN： MS Windows driver via SAPLPD
    在主机假脱机接受方法里选择 F(计算机前台打印);在 HOST PRINTER 输入打印服务器的 IP 地址
    然后保存就可以了.
- 2.将条码类型添加到设备类型里。T-CODE:SE73 --> 打印机条码
  - 在此会显示所有设备类型。请选择中打印机（输出设备）所在的设备类型。双击后，会显示此设备类型所能打印的条码类型。按 F5 对设备类型创建新的条码类型。
- 3.在 Smartforms 的样式里添加 条码样式。T-CODE:Smartforms
- 4.在 Smartforms 里，将样式应用到文本上。T-CODE:Smartforms
- 5.要对调整条码的样式，到设备类型，修 改打印控制（Print Control）的 SBPnn 的值 T-CDOE:SPAD--> 完全管理 --> 设备类型 --> 选择中使用的设备类型，双击。--> 打印控制
  - CCS (Control CharacterSequence) 的十六进制值，请使用软件：TBarCode_Studio.EXE 生成

**新方法：数据在 SAP 系统中生成条码图案，然后直接发送到打印机进行打印。**

```JS
1. 设置系统条码：SE73
 （1）进入SE73后选择系统条码。
 （2）点击更改按钮。
 （3）点击新建按钮。
 （4）选择已New方式建立条码。
 （5）输入Barcode Name和Short Text。
 （6）选择条码类型：例Code39。
 （7）设置Barcode Alignment： 选Normal。
 （8）设置Barcode parameters：在设置39码的Mod-43 Check Digit时，如果勾选该项，
      则打印出来的条码会自动在后面增加一位检查码。
 （9）Save。
```

### 设置SmartFom样式（Style）：

#### SmartForms。

- （1）进入SmartForms后选择样式（Style）。

- （2）在子元格式下建立一节点。

- （3）在标准设定View：选择刚才设定的条码。

- （4）字型：选择TWSONG  12pt.

- （5)  Save.

- （6）启用。

#### 在SmartForm设计时，字体选择为所设置的条码样式即可。

