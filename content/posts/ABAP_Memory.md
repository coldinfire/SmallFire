---
title: " SAP Memory 使用 "
date: 2019-02-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### SAP Memory 和 ABAP Memory ###
ABAP MEMORY：当活动的内部会话在单个主会话中调用另一个内部会话时，使用 export 和 import 参数传递数据。
SAP MEMORY：使用 set 和 get 参数将数据从一个主会话传递到另一个主会话。

![Memory Difference](/images/ABAP/ABAP_Memory2.png)

#### 使用的语句不同

SAP memory使用SET/GET parameters

  - SET PARAMETER ID 'MAT' FIELD var_matnr.

  - GET PARAMETER ID 'MAT' FIELD var_matnr.

ABAP Memory使用EXPORT 和IMPORT

- EXPORT p_matnr = var_matnr TO MEMORY ID **'memory_id'.**
- IMPORT p_matnr = var_matnr FROM MEMORY ID **'memory_id'.**

 FREE MEMORY ID 'ZTESTMAT'. => 清空指定的ABAP memory

 FREE MEMORY. => 清空External Session内的所有ABAP memory    

#### 共享范围不同

SAP memory用于所有external session间.

 ABAP memory用于同一个external session的internal session间。

#### 作用范围不同（就是生存期）

SAP memory在登陆到退出这期间一直有效。

ABAP memory只在同一个session(window) 内有效。

#### Dialog获取SAPMemory方式

在dialog 屏幕上建一个input field, 然后Parameter ID属性与'SAP_MMR'绑定,并打上2个勾。

Set Parameter: 允许将屏幕值返回给SAP Memory (类似于执行SET PARAMETER ID语句)

Get Parameter: 允许读取SAP Memory的值并默认显示(类似于执行GET PARAMETER ID语句).

### 创建共享内存对象类

1. 使用SE24 创建类 ZCL_ROOT_TEST

   ![SE24](/images/ABAP/ABAP_Memory.png)

2. 为共享内存对象区域创建area rool 类 (SHMA)

   - 启动事务 SHMA 并创建一个新的区域名称。（示例为 ZCL_MEM_AREA）
     填写 “内存共享区域” 的简短描述。在 “根类” 中，将您创建的类放在上方（ZCL_ROOT_TEST）。

   - 保存时，SAP 将生成与区域名称（ZCL_MEM_AREA）同名的区域类。新生成的 Area Class 将包含读取，写入和更新共享内存对象所需的所有方法。

3. 创建两个实例属性name,value，可见性设置为private，参考字段MEMORY_ID

4. 创建SET_DATA 和 GET_DATA方法

   1. **SET_DATA**

      ```js
      DATA: my_handle TYPE REF TO zcl_mem_area.
      DATA: my_root   TYPE REF TO zcl_root_test.
      TRY .
          CALL METHOD zcl_mem_area=>attach_for_write
            EXPORTING
              inst_name   = cl_shm_area=>default_instance
              attach_mode = cl_shm_area=>attach_mode_default
              wait_time   = 0
            RECEIVING
              handle      = my_handle.
          CREATE OBJECT my_root AREA HANDLE my_handle.
          CALL METHOD my_root->set_data
            EXPORTING
              name  = 'name_test'
              value = 'value_test'.
          CALL METHOD my_handle->detach_commit.
        CATCH cx_shm_exclusive_lock_active .
        CATCH cx_shm_version_limit_exceeded .
        CATCH cx_shm_change_lock_active .
        CATCH cx_shm_parameter_error .
        CATCH cx_shm_pending_lock_removed .
      ENDTRY.
      ```
   
   2. **GET_DATA**
   
      ```JS
      DATA: my_handle TYPE REF TO zcl_mem_area.
      DATA: my_root   TYPE REF TO zcl_area_root.
   
      TRY .
       CALL METHOD zcl_mem_area=>attach_for_read
            EXPORTING
              inst_name = zcl_mem_area=>attach_for_write
            RECEIVING
              handle    = my_handle.
          CREATE OBJECT my_root AREA HANDLE my_handle.
          CALL METHOD my_root->get_data
            IMPORTING
              name  = lv_name
              value = lv_value.
          CALL METHOD my_handle->detach_commit.
         CATCH cx_shm_inconsistent .
         CATCH cx_shm_no_active_version .
         CATCH cx_shm_read_lock_active .
         CATCH cx_shm_exclusive_lock_active .
         CATCH cx_shm_parameter_error .
         CATCH cx_shm_change_lock_active .
      ENDTRY.
      ```
   
5. 使用该类进行传递Memory Id

   

