---
title: " Web Dynpro 在Portal上传输 "
date: 2018-11-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

当在Portal测试环境测试通过后，需要将其从测试环境搬到正式环境.

### Download from QAS system

#### Open Transport package export Editor

选择System Administration,然后选择Transport,再选择Transport Packages -> Export导出.

#### Export Transport package

选择需要导出的Transport package,右键 Export -> Standard Export.

![Transfer](/images/webdynproABAP/Portal16.png)

选择要Export的内容，然后点Include inExport 或则选择不需要的，点击Exclude from export.然后点击Next.

![Transfer](/images/webdynproABAP/Portal17.png)

直接选择Next.

![Transfer](/images/webdynproABAP/Portal18.png)

直接选择Export.

![Transfer](/images/webdynproABAP/Portal19.png)

将要传输的内容给Download下来了，接下来只需要将这个文件Upload到正式环境即可。

![Download](/images/webdynproABAP/Portal20.png)

### Upload to PRD system

- 登录到生产服务器,选择System Administration,然后选择Transport,再选择Import导入。

- Source for package files: 选择 Client,从本地导入文件

- Browse：选择之前导出的文件路径和对应的.epa文件

- Upload: 点击Upload按钮，会在Import Preview中看到将要导入的内容

- 确认无误后，点击Import按钮，等待导入完成即可

![Upload](/images/webdynproABAP/Portal15.png)

### 传输完成后需要在R3 系统进行访问的激活

Tcode: SICF

![R3 PRD](/images/webdynproABAP/Portal21.png)

选择服务器访问路径：sap/bc/webdynpro/sap/Application name/

![R3 PRD](/images/webdynproABAP/Portal22.png)

Activate Service:        

![R3 PRD](/images/webdynproABAP/Portal23.png)