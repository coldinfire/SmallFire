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

- Leave to screen 0.：Exits back to first screen

- Call screen 100. ：Calls screen 100

- Set screen 100.：Reassigns next screen value
- Leave screen. ：Leaves to next screen

- Leave program. ：Leaves Program)

#### Dialog GUI Components

- Menu Bar 
- Standard Toolbar 
- Application Toolbar 
- Key Settings 

#### GUI Status Declaration

```ABAP
SET PF-STATUS <ZSTATUS>. 
SET TITLEBAR <NNN>.	" i.e. 100 "

SET pf-status 'STATUS' EXCLUDING 
" Exclude multiple function codes. Itab would look like this: "
Data: Begin of itab occurs 0. 
  Fcode like sy-ucomm. 
Data: End of itab. 

SET pf-status SPACE. "To deactivate previous status and activate default list" 
SET pf-status 'STATUS' WITH . 
```

**Function Codes** 
Function Type: 
' '	Normal 
'E'	Exit 
'S'	System 
'T'	Transaction 
'P'	Tabst. Control 
'H'	Help request 