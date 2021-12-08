---
title: " PP 模块常用BAPI "
date: 2019-12-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### 生产计划的 BAPI 列表

#### Routing：工艺路线

| BAPI                         | Description             |
| :--------------------------- | :---------------------- |
| BAPI_ROUTING_CREATE          | 创建工艺路线BAPI - CA03 |
| BAPI_ROUTING_EXISTENCE_CHECK | 检查工艺路线是否存在    |

#### Reference operation set：参考操作集

| BAPI                           | Description        |
| :----------------------------- | :----------------- |
| BAPI_REFSETOFOPERATIONS_CREATE | 创建参考参考工序集 |
| BAPI_REFSETOFOPR_EXISTENCE_CHK | 检查参考参考工序集 |

#### Planned order：计划订单

| BAPI                           | Description                     |
| :----------------------------- | :------------------------------ |
| BAPI_PLANNEDORDER_CHANGE       | 更改计划订单 - MD04             |
| BAPI_PLANNEDORDER_CREATE       | 创建计划订单                    |
| BAPI_PLANNEDORDER_DELETE       | 删除计划订单                    |
| BAPI_PLANNEDORDER_EXIST_CHECK  | 检查计划订单是否存在            |
| BAPI_PLANNEDORDER_GET_DETAIL   | 获取计划订单详细信息(计划订单） |
| BAPI_PLANNEDORDER_GET_DET_LIST | 获得计划订单信息                |

#### Planned Independent Requirement：计划独立需求

| BAPI                        | Description             |
| :-------------------------- | :---------------------- |
| BAPI_REQUIREMENTS_CHANGE    | 更改计划独立需求 - MD61 |
| BAPI_REQUIREMENTS_CREATE    | 创建计划独立需求        |
| BAPI_REQUIREMENTS_GETDETAIL | 显示计划独立需求        |

#### Production order：生产订单

| BAPI                           | Description          |
| :----------------------------- | :------------------- |
| BAPI_PRODORD_WM_MAT_STAGING    | WM材料分期           |
| BAPI_PRODORD_SETUSERSTATUS     | 设置用户状态         |
| BAPI_PRODORD_SET_DEL_INDICATOR | 设置删除标识         |
| BAPI_PRODORD_SET_DELETION_FLAG | 设置删除标识         |
| BAPI_PRODORD_SCHEDULE          | 进行调整             |
| BAPI_PRODORD_REVOKEUSERSTATUS  | 取消用户状态         |
| BAPI_PRODORD_RELEASE           | 发布                 |
| BAPI_PRODORD_GET_LIST          | 列表抬头订单         |
| BAPI_PRODORD_GET_DETAIL        | 抬头订单明细         |
| BAPI_PRODORD_EXIST_CHECK       | 确认检查性           |
| BAPI_PRODORD_CREATE            | 创建生产订单         |
| BAPI_PRODORD_CREATE_FROM_REF   | 创建根据模板         |
| BAPI_PRODORD_CREATE_FROM_PLORD | 创建带有计划订单     |
| BAPI_PRODORD_CREATE_CAP_REQ    | 产生能力需求         |
| BAPI_PRODORD_COSTING           | 创建成本估计         |
| BAPI_PRODORD_COMPLETE_TECH     | Complete & Tech 设置 |
| BAPI_PRODORD_CLOSE             | 关闭订单             |
| BAPI_PRODORD_CHECK_MAT_AVAIL   | 检查物料可用性       |
| BAPI_PRODORD_CHANGE            | 更改生产订单         |

#### Production order confirmation：生产订单确认

| BAPI                           | Description          |
| :----------------------------- | :------------------- |
| BAPI_PRODORDCONF_GET_TT_PROP   | 确认计工单           |
| BAPI_PRODORDCONF_GET_TE_PROP   | 确认计工单           |
| BAPI_PRODORDCONF_GETLIST       | 生产订单确认         |
| BAPI_PRODORDCONF_GETDETAIL     | 生产订单确认详细信息 |
| BAPI_PRODORDCONF_GET_HDR_PROP  | 确认计划订单         |
| BAPI_PRODORDCONF_EXIST_CHK     | 检查工单是否存在     |
| BAPI_PRODORDCONF_CREATE_TT     | 确认计划工单         |
| BAPI_PRODORDCONF_CREATE_TE     | 确认计划工单         |
| BAPI_PRODORDCONF_PDC_UPLOAD_TT | PP 确认计划工单      |
| BAPI_PRODORDCONF_PDC_UPLOAD_TE | PP 确认计划单        |
| BAPI_PRODORDCONF_CREATE_HDR    | 输入订单确认         |
| BAPI_PRODORDCONF_CREATE_ACT    | 输入订单激活确认     |
| BAPI_PRODORDCONF_CANCEL        | 取消生产订单         |

#### BDC Download & Upload for production order：下载和上传的BDC的生产订单

| BAPI                           | Description          |
| :----------------------------- | :------------------- |
| BAPI_RCVPRORDCF_RECEIVEPRODORD | PP-PDC: 下载生产订单 |
| BAPI_RCVPRORDCF_RECEIVEWORKC   | PP-PDC: 下载工作中心 |
| BAPI_RCVPRODCF_REQUEST_CONF    | PP-PDC: 上传请求     |

#### KANBAN：看板

| BAPI                           | Description              |
| :----------------------------- | :----------------------- |
| BAPI_KANBAN_CHANGE             | 更改KANBAN数据           |
| BAPI_KANBAN_CHANGESTATUS       | 更改KANBAN状态           |
| BAPI_KANBAN_CHANGESTATUS1      | 更改KANBAN状态           |
| BAPI_KANBAN_GETLIST            | 匹配选择标准KANBAN的测定 |
| BAPI_KANBAN_GETLIST_ALL        | 匹配选择标准KANBAN的测定 |
| BAPI_KANBAN_GETLISTFORSUPPLIE1 | 为供应商提供KANBAN数据   |
| BAPI_KANBAN_GETLISTFORSUPPLIER | 为供应商提供KANBAN数据   |
| BAPI_KANBAN_SETINPROCESS       | 为供应商提供KANBAN数据   |

#### KANBAN CONTROL CYCLE：看板

| BAPI                           | Description                  |
| :----------------------------- | :--------------------------- |
| BAPI_KANBANCC_ADDEVENTDRKANBAN | 为控制周期创建事件驱动的看板 |
| BAPI_KANBANCC_CHANGE           | 变更控制周期                 |
| BAPI_KANBANCC_CREATE           | 创建控制周期                 |
| BAPI_KANBANCC_DELETE           | 删除控制周期                 |
| BAPI_KANBANCC_EXISTCHECK       | 检查控制循环的存在           |
| BAPI_KANBANCC_GETLIST          | 使用选择标准确定看板控制周期 |
| BAPI_KANBANCC_GETLIST_ALL      | 使用选择标准确定看板控制周期 |
| BAPI_KANBANCC_WITHDRAWQUANTITY | 看板控制周期的数量信号       |

#### REM Confirmation：REM 确认

| BAPI                        | Description                |
| :-------------------------- | :------------------------- |
| BAPI_REPMANCONF_CANCEL      | 处理的重复制造取消         |
| BAPI_REPMANCONF1_CANCEL     | 处理的重复制造取消         |
| BAPI_REPMANCONF_CREATE_MTO  | 销售订单执行重复制造情况   |
| BAPI_REPMANCONF1_CREATE_MTO | 销售订单执行重复制造情况   |
| BAPI_REPMANCONF_CREATE_PLOT | 执行生产成本               |
| BAPI_REPMANCONF1_CREATE_MTP | 在很多情况下，执行生产成本 |
| BAPI_REPMANCONF_CREATE_MTS  | 在很多情况下，执行生产成本 |
| BAPI_REPMANCONF1_CREATE_MTS | 在很多情况下，执行生产成本 |
| BAPI_REPMANCONF_EXIST_CHK   | 检查对象存在               |
| BAPI_REPMANCONF1_EXIST_CHK  | 检查对象存在               |