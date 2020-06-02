---
title: "开发 常用Tcode汇总"
date: 2018-08-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



### ABAP TCode ###

| 事务码                  | 功能                           | 事务码 | 功能                         |
| :---------------------- | :----------------------------- | ------ | ---------------------------- |
| SPRO                    | 显示后勤                       | SE18   | BADI Builder                 |
| ST05/SE30               | 系统跟踪/系统性能详细跟踪      | SE19   | BADI Implement               |
| ST22                    | 查看日志信息                   | SE24   | 创建、修改、查询类对象       |
| SM12                    | 编辑锁定解除                   | SE21   | 创建、修改、查询包的开发工具 |
| SMW0                    | 上传EXCEL模板                  | SE91   | 定义和查看消息               |
| LSMW                    | 批导                           | SE93   | 创建独立的事物码             |
| SM35                    | LSMW Run Batch Input Session   | SE16N  | 修改透明表&sap_edit          |
| SHDB                    | BDC录屏                        | SM30   | 维护表视图                   |
| SOAMANAGER              | SAP 发布 WS                    | SHD0   | 事务和屏幕变式               |
| SQ03/SQ02/SQ01    /SQVI | Query 报表                     | SLIN   | ABAP 扩展语法检查            |
| SM21                    | 系统日志，记录系统的事件和问题 | SP01   | 打印需求查看                 |
| AL08                    | 查看当前活动用户               | RZ01   | 图形化的JOB监视              |
|                         |                                |        |                              |
|                         |                                |        |                              |



#### BASIS TCode

| 事务码    | 功能                 | 事务码 | 功能                         |
| --------- | -------------------- | ------ | ---------------------------- |
| DB02      | 分析表和索引         | SU01   | 用户账号权限                 |
| DB14      | 显示 SAPDBA 行为记录 | SUIM   | 查询用户的信息以及权限的分配 |
| DB20      | 刷新表统计           | PFCG   | 角色维护                     |
| AL11      | SAP 服务器目录管理   | SU20   | 权限字段                     |
| SM04      | 用户清单 踢人        | SU21   | 权限对象                     |
| SM13      | 查看 SAP update 进度 | SU22   | 根据事务码查看权限对象       |
| SM37/SM36 | 后台 JOB             | ST01   | 事务码权限对象及权限点追踪   |
| SM21      | SAP 系统日志         | SU53   | 检查权限                     |
|           |                      |        |                              |

