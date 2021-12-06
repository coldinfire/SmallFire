---
title: " 开发常用事物码汇总 "
date: 2018-08-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### ABAP 常用  TCode ###

| TCode                   | Description                    | TCode | Description                  |
| :---------------------- | :----------------------------- | ----- | ---------------------------- |
| SPRO                    | 显示后勤                       | SE18  | BADI Builder                 |
| ST05/SE30               | 系统跟踪/系统性能详细跟踪      | SE19  | BADI Implement               |
| ST22                    | 查看日志信息                   | SE24  | 创建、修改、查询类对象       |
| SM12                    | 编辑锁定解除                   | SE21  | 创建、修改、查询包的开发工具 |
| SMW0                    | 上传EXCEL模板                  | SE91  | 定义和查看消息               |
| LSMW                    | 批导                           | SE93  | 创建独立的事物码             |
| SM35                    | LSMW Run Batch Input Session   | SE16N | 修改透明表&sap_edit          |
| SHDB                    | BDC录屏                        | SM30  | 维护表视图                   |
| SOAMANAGER              | SAP 发布 WS                    | SHD0  | 事务和屏幕变式               |
| SQ03/SQ02/SQ01    /SQVI | Query 报表                     | SLIN  | ABAP 扩展语法检查            |
| SM21                    | 系统日志，记录系统的事件和问题 | SP01  | 打印需求查看                 |
| AL08                    | 查看当前活动用户               | RZ01  | 图形化的JOB监视              |

### Background Processing

| TCode   | Description                       | TCode | Description                         |
| ------- | --------------------------------- | ----- | ----------------------------------- |
| RZ01    | Job Scheduling Monitor            | SM39  | Job Analysis                        |
| SM36    | Schedule Background Job           | SM65  | Background Processing Analysis Tool |
| SM36WIZ | Job definition wizard             | SMX   | Display Own Jobs                    |
| SM37    | Overview of job selection         | RZ15  | Read XMI log                        |
| SM37B   | Simple version of job selection   | SM61  | Backgroup control objects monitor   |
| SM37BAK | Old SM37 backup                   | SM61B | New control object management       |
| SM37C   | Flexible version of job selection |       |                                     |

### Performance Analysis

| TCode     | Description                          | TCode   | Description                        |
| --------- | ------------------------------------ | ------- | ---------------------------------- |
| STAD      | Statistics display for all systems   | ST04    | DB Performance Monitor             |
| STAT      | Local Transaction Statistics         | ST04N   | Database Performance Monitor       |
| STATTRACE | Global Statistics & Traces           | STP4    | Select DB activities               |
| STUN      | Menu Performance Monitor             | ST04RFC | MS SQL Server Remote Monitor tools |
| ST02      | Steups/Tune Buffers                  | ST05    | Performance trace                  |
| ST03      | Performance,SAP Statistics, Workload | ST06    | Operating System Monitor           |
| ST03G     | Global Workload Statistics           | ST07    | Application monitor                |
| ST03N     | R/3 Workload and Perf. Statistics    | ST10    | Table Call Statistics              |

### Spool & Print

| TCode | Description             | TCode | Description             |
| ----- | ----------------------- | ----- | ----------------------- |
| SP00  | Spool and related areas | SP11  | TemSe directory         |
| SP01  | Output Controller       | SP12  | TemSe Administration    |
| SP01O | Spool Controller        | SP1T  | Output Control (Test)   |
| SP02  | Display Spool Requests  | SPAD  | Spool Administration    |
| SP02O | Display Output Requests | SPCC  | Spool consistency check |
| SP03  | Spool: Load Formats     |       |                         |

### Transport System

| TCode      | Description                    | TCode       | Description                    |
| ---------- | ------------------------------ | ----------- | ------------------------------ |
| SE01       | Transport Organizer (Extended) | STMS_DOM    | TMS System Overview            |
| SE03       | Transport Organizer Tools      | STMS_FSYS   | Maintain TMS system lists      |
| SE06       | Set Up Transport Organizer     | STMS_IMPORT | TMS Import Queue               |
| SE07       | CTS Status Display             | STMS_INBOX  | TMS Worklist                   |
| SE09       | Transport Organizer            | STMS_MONI   | TMS Import Monitor             |
| SE10       | Transport Organizer            | STMS_PATH   | TMS Transport Routes           |
| SEPA       | EPS Server: Administration     | STMS_QA     | TMS Quality Assurance          |
| SEPS       | SAP Electronic Parcel Service  | STMS_QUEUES | TMS Import Overview            |
| STMS       | Transport Management System    | STMS_TCRI   | Display/Maintain Table TMSTCRI |
| STMS_ALERT | TMS Alert Monitor              | STMS_TRACK  | TMS Import Tracking            |

### System Monitoring

| TCode | Description                         | TCode         | Description                    |
| ----- | ----------------------------------- | ------------- | ------------------------------ |
| SM50  | Work Process Overview               | ST22          | ABAP dump analysis             |
| SM51  | List of SAP Systems                 | ST22OLD       | Old Dump Analysis              |
| SM66  | Systemwide Work Process Overview    | SM35          | Batch Input Monitoring         |
| STDA  | Debugger display/control (server)   | SMMS          | Message Server Monitor         |
| RZ02  | Network Graphics for SAP Instances  | RZ23          | Performance database monitor   |
| RZ03  | Presentation, Control SAP Instances | RZ23N         | Central Performance History    |
| RZ04  | Maintain SAP Instances              | RZ25          | Start Tools for a TID          |
| RZ06  | Alerts Thresholds Maintenance       | RZ26          | Start Methods for an Alert     |
| RZ08  | SAP Alert Monitor                   | RZ27          | Start RZ20 for a Monitor       |
| RZ20  | CCMS Monitoring                     | RZ27_SECURITY | MiniApp CCMS Alerts Security   |
| RZ21  | CCMS Monitoring Arch. Customizing   | RZ28          | Start Alert Viewer for Monitor |

### BASIS TCode

| TCode     | Description          | TCode | Description                  |
| :-------- | :------------------- | :---- | :--------------------------- |
| DB02      | 分析表和索引         | SU01  | 用户账号权限                 |
| DB14      | 显示 SAPDBA 行为记录 | SUIM  | 查询用户的信息以及权限的分配 |
| DB20      | 刷新表统计           | PFCG  | 角色维护                     |
| AL11      | SAP 服务器目录管理   | SU20  | 权限字段                     |
| SM04      | 用户清单 踢人        | SU21  | 权限对象                     |
| SM13      | 查看 SAP update 进度 | SU22  | 根据事务码查看权限对象       |
| SM37/SM36 | 后台 JOB             | ST01  | 事务码权限对象及权限点追踪   |
| SM21      | SAP 系统日志         | SU53  | 检查权限                     |

