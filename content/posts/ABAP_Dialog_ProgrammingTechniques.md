---
title: " Dialog 编程技术 "
date: 2018-09-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---

### ABAP Dialog 编程技术

#### 模块池对象的命名约定：BC410

- SAPMV45A：Program name
- MV45A**F01**：Form include 
- MV45A**O01**：PBO include 
- MV45A**I01**：PAI include 
- MV45A**TOP**：Data declaration 
- SAPZNAME：User defined Program 
- ZNAMEF01：User defined Form include 

#### Basic Screen Processing 

- `Leave to screen 0.`：Exits back to first screen
- `Leave to screen 200`：Leaves to screen 20
- `Call screen 100. `：Calls screen 100
- `Set screen 100.`：Reassigns next screen value
- `Leave screen. `：Leaves to next screen
- `Leave program. `：Leaves Program)

#### Screen numbers

- 大于 9000 的数字保留给 SAP 客户自定义屏幕
- 数字 1000 到 1010 保留用于 ABAP 字典表的维护屏幕和标准选择屏幕报告

#### Dialog GUI Components

- Menu Bar 
- Standard Toolbar 
- Application Toolbar 
- Key Settings 

#### GUI Status Declaration

```ABAP
SET PF-STATUS <ZSTATUS>. 
SET TITLEBAR <TITLE>.	" i.e. 100 "
"Exclude multiple function codes. "
Data: Begin of ex_tab occurs 0. 
  fcode like sy-ucomm. 
Data: End of itab. 
ex_tab-fcode = '&ODN'. APPEND ex_tab. "升序"
ex_tab-fcode = '&OUP'. APPEND ex_tab. "降序"
SET PF-STATUS 'STATUS' EXCLUDING ex_tab. 
"To deactivate previous status and activate default list"
SET PF-STATUS SPACE.  
```

**Function Codes** 

Function Type:

-  ' '：Normal 
- 'E'：Exit 
- 'S'：System 
- 'T'：Transaction 
- 'P'：Tabst. Control 
- 'H'：Help request 

#### Get field clicked on

```ABAP
GET CURSOR FIELD fieldname [LINE] fieldname or structure(store rep line).
```

*CURSOR Position*

- Within the ABAP(PBO)：`SET CURSOR field 'SFLIGHT-CONNID'. `
- Within screen attributes (Settings) 

