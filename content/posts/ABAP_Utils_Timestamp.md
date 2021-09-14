---
title: "时间戳操作"
date: 2018-12-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### SAP 时间戳

#### 获取时间戳方法

- `GET TIME STAMP FIELD time_stamp.`

  ```ABAP
  DATA time_stamp TYPE tzntstmps. "UTC Time Stamp in Short Form (YYYYMMDDhhmmss)"
  GET TIME STAMP FIELD time_stamp.
  ```

- BAPI：`IB_CONVERT_INTO_TIMESTAMP`

  ```ABAP
  CALL FUNCTION 'IB_CONVERT_INTO_TIMESTAMP'
    EXPORTING
      i_datlo     = sy-datum
      i_timlo     = sy-uzeit
      i_tzone     = sy-zonlo "Time Zone of Current User"
    IMPORTING
      e_timestamp = time_stamp.
  ```

#### 解析时间戳方法

- `CONVERT TIME STMP .`

  ```ABAP
  DATA:lv_date TYPE dats,
       lv_time TYPE tims,
       time_stamp TYPE tzntstmps.
  CONVERT TIME STAMP time_stamp TIME ZONE sy-zonlo INTO DATE lv_date TIME lv_time.
  ```

- BAPI：`IB_CONVERT_FROM_TIMESTAMP`

  ```ABAP
  DATA: lv_datlo LIKE  sy-datlo, "Local Date for Current User"
        lv_timlo LIKE  sy-timlo, "Local Time for Current User"
        time_stamp TYPE tzntstmps.
  CALL FUNCTION 'IB_CONVERT_FROM_TIMESTAMP'
    EXPORTING
      i_timestamp = time_stamp
      i_tzone     = sy-zonlo "Time Zone of Current User"
    IMPORTING
      e_datlo     = lv_datlo
      e_timlo     = lv_timlo.
  ```

#### 获取用户时区

- 时区表：`TTZZ`

- BAPI：`TZON_GET_USER_TIMEZONE`

  ```ABAP
  DATA: lv_zonlo TYPE sy-zonlo.
  CALL FUNCTION 'TZON_GET_USER_TIMEZONE'
  EXPORTING
      if_username                   = sy-uname
    IMPORTING
      ef_timezone                   = lv_zonlo
    EXCEPTIONS
      no_timezone_customizing       = 1
      no_valid_user                 = 2
      others                        = 3.
  ```
  
  