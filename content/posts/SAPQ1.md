---
title: "移库动作产生的报错:Error BS001 when posting goods movment"
date: 2019-04-14
draft: false
author: Small Fire
categories: 
  - ABAP

tags: 
  - QA

---



Q：当进行移库操作时，同一个程序中执行多次移库动作会产生Error Message:No status object is available for HU  XXXXXX?

A：需要每次进行移库前刷新数据：

- "调用BAPI进行移库操作
      

  - CALL FUNCTION 'HU_PACKING_REFRESH'.
    
  - CALL FUNCTION 'SERIAL_INTTAB_REFRESH'.
    
  - CALL FUNCTION 'V51G_REFRESH'.

-  

  ```JS
  CALL FUNCTION 'HU_CREATE_GOODS_MOVEMENT'
    EXPORTING
      IF_EVENT       = '0006'
      IF_COMMIT      = ''
      IF_TCODE       = 'HUMO'
      IT_MOVE_TO     = IT_MOVE_TO[]
      IT_EXTERNAL_ID = IT_EXTERNAL_ID[]
    IMPORTING
      ES_MESSAGE     = ES_MESSAGE
      ET_MESSAGES    = ET_MESSAGES[]
      ES_EMKPF       = ES_EMKPF.
  ```

  