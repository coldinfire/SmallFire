---
title: " 物料主数据增强自定义字段 "
date: 2019-03-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 自定义增强字段

正常情况下通过FM Exits来增强一个屏幕的步骤：

1. Find relevant enhancement thru program name.

2. T-code: `CMOD`, create an enhancement project, then assign the enhancement to the enhancement project

3. Create a subscreen in corresponding X function group

4. Implement the FM exits for transporting data to subscreen and retrieving data from subscreen.

5. Active the enhancement project.

在main screen的flow logic中

- **PBO**

  ```JS
  CALL CUSTOMER-SUBSCREEN <area> INCLUDING <x-function-pool> <screen_number>.
    "Transporting data to subscreen"
    MODULE ...
      CALL CUSTOMER-FUNCTION 'xxx'
        EXPORTING
          i_vars = gl_field.
  ```

- **PAI**

  ```JS
  CALL CUSTOMER-SUBSCREEN <area> .
    "Transporting data from subscreen"
    MODULE...
       CALL CUSTOMER-FUNCTION 'xxx'
         IMPORTING
            o_vars = gl_field.
   
  ```

   这里的FM 的parameter interface是不能改变的，且每一个screen enhancemnet都要对应一个确定的自定义的 XXX Function Group但screen number是可以变化的。



