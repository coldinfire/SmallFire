---
title: "报表开发(四)"
date: 2018-09-19T17:20:58+08:00
draft: true
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

---

## 执行程序的使用范围，报表事件
```JS
LOAD-OF-PROGRAM.
INITIALIZATION.          （Before display the selection screen）
  AT-SELECTION SCREEN ON fiedl.（在PAI事件结束后执行，进行校验和检查输入值）
  AT SELECTION-SCREEN ON VALUE-REQUEST FOR Z_XXX.
AT SELECTION-SCREEN. //After enter the option data check
   PERFORM check_input.
START-OF-SELECTION.//Begin the main programer
  xxxx
END-OF-SELECTION.
Interactive Eventrs. (User for interactive reporting)
```
## 报表程序大体逻辑结构
​	**抬  头:** 报表的主要信息(抬头信息)

​	**行项目:** 查询出的每行记录信息

​	**明  细:** 每个行项目出关键字外其他明细内容

## 字段符号

**定义：** FIELD-SYMBOLS <FS> LIKE structure.

​    <1> FS必须和某个变量，结构或者内表绑定后才能使用，这点和C语言里的指针
​     （在ABAP里最接近指针的是TYPE REF TO）不同，在使用FS前必须分配给某个变量，不然会发生
​      FS未分配的运行时错误。如果LOOP内表时ASSIGNING到FS，之后假如有REFRESH内表的操作的话，
​      FS也会再次回到初始未被ASSIGN的状态，这时如果使用FS也会发生FS未分配的RUNTIME ERROR。
​    <2> ASSIGN structure TO <fs>:将某个内存区域分配给字段符号，这样字段符号就代表了该内存
​      区域，即该内存区域别名字段符号可以看作仅是已经被解引用的指针，即某个变量的别名。
  ASSIGN <Val> TO <fs>: 将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域。
  UNASSIGN: 该语句是初始化<FS>字段符号，执行后字段符号将不再引用内存区域，
​            <fs> is assigned返回假。
  CLEAR: 与UNASSIGN不同的是，只有一个作用就是初始化它所指向的内存区域，而不是解除分配。
3.2 LOOP内表INTO结构（工作区）和LOOP内表ASSIGNING<结构>的比较
​    LOOP内表INTO结构时，系统会把先把当前行的数据复制到结构，如果结构的值改了，还需要使用
  MODIFY语句把更改后的值传回内表。也就是说，结构是内表里的数据的一个副本，操作这个副本不会
  影响内表里的数据。为了提高效率，可以使用FS，FS直接指向内表数据，省去了复制数据到结构的过程
  修改FS的值也就是相当于直接修改内表里的数据，不需要再使用MODIFY语句。
3.3 READ TABLE内表
3.4 ASSIGN隐式强转
TYPES: BEGIN OF t_date,
​    year(4) TYPE  n,
​        month(2) TYPE n,
​    day(2) TYPE n,
  END OF t_date.
FIELD-SYMBOLS <fs> TYPE t_date."将<fs>定义成了具体限定类型
ASSIGN sy-datum TO <fs> CASTING. "后面没有指定具体类型，所以使用定义时的类型进行隐式转换
3.5 ASSIGN显示强转
  DATA txt(8) TYPE c VALUE '19980606'.
  FIELD-SYMBOLS <fs>.
  ASSIGN txt TO <fs> CASTING TYPE d."由于定义时未指定具体的类型，所以这里需要显示强转
3.6 动态引用，通过循环赋值给定义的字段符号，对其进行修改，等于直接修改原内表。
  field-symbols:<l_shortageqty> type mng01.
  loop at <dyn_table> assigning <dyn_wa>.
​    assign component 'SHORTAGEQTY' of structure <dyn_wa> to <l_shortageqty>.
​    <l_shortageqty> = <l_shortageqty> - <l_fvalue>.

## 选择屏幕 

[引用链接](https://www.cnblogs.com/foxting/archive/2012/07/01/2572243.html)

    <1>SELECT-SCREEN 框架结构
      SELECT-SCREEN语句用于创建屏幕的框架结构,主要包括屏幕元素的创建、子屏幕的创建等。
      定义屏幕对象：
        SELECTION-SCREEN BEGIN OF SCREEN src.
          .......
        SELECTION-SCREEN END OF SCREEN src.
        该语法用于定义一个INCLUDE SUREEN，可通过CALL方法在Report程序中引用。
       CALL SCREEN <num> STARTING AT [1 2] ENDING AT [1 2].
       参数可以将所定义屏幕窗体作为一个新的对话框窗体来引用，并指定期创建的具体大小及位置。
      注意：当从一个主屏幕中来调用其程序中的另一窗体时，必须使用CALL SELECTION-SCREEN方法.
      定义屏幕块：
        SELECTION-SCREEN BEGIN OF BLOCK block.
          ......
        SELECTION-SCREEN END OF BLOCK.
        该语法在屏幕中定义一个BLOCK，其扩展语法包括：
          ...WITH FRAME：创建一个框架
          ...TITLE title：创建一个带标题的框架。
          ...NO INTERVALS：所创建的框架中限制SELECT只有一个输入项。 
     其他元素：
      SELECTION-SCREEN INCLUDE BLOCKS <block1>.  调用已存在屏幕元素
      SELECTION-SCREEN ULINE.  划出横线，必须用在BLOCK中才能生效。
      SELECTION-SCREEN SKIP <n>.  在BLOCK中产生换行<n>。
      SELECTION-SCREEN POSITION <pos>. 在BLOCK中产生空格。
      SELECTION-SCREEN BEGIN OF LINE.
        ......
      SELECTION-SCREEN END OF LINE. 将所生成的屏幕元素控制在一行.
    <2>PARAMETERS 单值输入
      PARAMETERS 参照数据字典具体字段或者自定义数据类型创建单值文本输入域以及单选/复选框等：
      PARAMETERS: <P_1> LIKE <field>    "文本域
                TYPE AS CHECKBOX.       "复选框
                RADIOBUTTON GROUP GRP1, "单选域 
                P2 RADIOBUTTON GROUP GRP1 DEFAULT 'X',  "默认选中 ”X“
                P3 RADIOBUTTON GROUP GRP1. "GRP1单选组
      默认值：
        ...DEFAULT f:定义默认值。
        ...TYPE type:参照某一类型对象定义PARAMETERS。
        ...DECIMALS dec：定义小数位，对输入参数自动格式化，该语法只对P类型有效(参数某一类型定义关键字TYPE)。 
        ...LIKE g：參照某一字典对象定义PARAMETERS。
        ...MEMORY ID pi：将PARAMETERS存储在SAP内存，参数名长度不能超过三位。
        ...NO-DISPLAY：将PARAMETERS设置为隐藏，不会的屏幕上输出。
        ...LOWER CASE：输入值中不允许输入小写字符，否则会自动转换为大写。
        ...OBLIGATORY：限制该PARAMETERS为必填，否则会提示输入。
        ...AS CHECKBOX：创建CHECKBOX对象。
        ...RADIO BUTTON GROUP radi：创建（RADIO）单选框。
        ...VISIBLE LENGTH vlen：定义显示长度。
        ...USER-COMMAND ucom：为创建对象分配对象名，该值保存在内表中可供其它对象操作。
        ...AS LISTBOX VISIBLE LENGTH vlen：创建一个下拉框，并指定长度。
     下拉框实现：
      *定义一个下拉框对象，其可视数据长度一般比输出数据长度大2用于放置下拉图标
        PARAMETERS:P_LANG(20) AS LISTBOX VISIBLE LENGTH 22.
      *该变量用于记录下拉列表数值是否初始化，否则每次屏幕初始化都会重新加载重复数据
        DATA:INIT.
        AT SELECTION-SCREEN OUTPUT.
        PERFORM SETLIST.
      *子程序用于加载下拉框的数据
        FORM SETLIST.
          TYPE-POOLS VRM. 
          DATA MYVALUE TYPE VRM_VALUES WITH HEADER LINE.
        *对内表加载值
          MYVALUE-KEY = 'CHINESE'. MYVALUE-TEXT = '中国'.    
          APPEND MYVALUE.
          MYVALUE-KEY = 'AMERICAN'. MYVALUE-TEXT = '美国'.         
          APPEND MYVALUE.
          MYVALUE-KEY = 'ENGLISH'. MYVALUE-TEXT = '英国'. 
          APPEND MYVALUE.
          MYVALUE-KEY = 'FRENCH'. MYVALUE-TEXT = '法国'.   
          APPEND MYVALUE.
        IF INIT IS INITIAL.
            CALL FUNCTION 'VRM_SET_VALUES' "调用函数对下拉框对象传递数据
              EXPORTING
                ID = 'P_LANG' "下拉框对象名
                VALUES = MYVALUE[]  "下拉框中加载的数据
             EXCEPTIONS
               ID_ILLEGAL_NAME = 1
               OTHERS = 2.
        ENDIF.
        INIT = 'X'. "记录初始化状态
      ENDFORM.
    <3> SELECT-OPTIONS 输入域
      SELECT-OPTIONS 参照数据库字段来建立输入域，命名不能超过8位，最大输入长度为18位：
      SELECT-OPTIONS： <S_1> FOR <dbtab-ele>.... .
      内表结构：
        SIGN：I , E
        OPTION: EQ,NE,CP（模糊查询）,NP,GE,LT,LE,GT
        LOW : 范围较小值
        HIGH: 范围较大值
      默认值设定：
        ...DEFAULT g:定义单一默认值。
        ...DEFAULT g...OPTION  xxx ... SIGN s:定义含判断条件的单一默认值。
        ...DEFAULT g TO h:定义默认值的取值范围。
        ...DEFAULT g TO h ... OPTION op ... SIGN s:设置默认值的聚会范围及判断条件。
        ...MEMORY ID pid:将SELECT-OPTIONS分配参数名并存储在SAP内存，参数名长度不能超过三位。
        ...NO-DISPLAY:将SELECT-OPTIONS设置为隐藏，不会在屏幕上输出。
        ...LOWER CASE:输入值中不允许输入小写字符，否则会自动转换为大写。
        ...OBLIGATORY:限制该SELECT-OPTIONS为必须输入的项目，执行中系统会提示。
        ...NO-EXTENSION:限制该SELECT-OPTIONS只能输入一行数据，输入多行按钮（上图最右边按钮）被隐藏。
        ...VISIBLE LENGTH vlen:定义所显示数据的长度。
     <4> GOTO-->Text Elements   (TCode:SE32)
        前台界面显示的为PARAMETERS和SELECTION-OPTION定义的字段，不便于理解需。
        提供某一字段的完整名称以方便用户理解。
        GOTO -->Translation：可进行多语言显示的维护
      包含字段：
        Selection Texts：定义已存在并且激活的屏幕元素的名称。
        Text Symbols：实现自定义文本及符号,该文本使用对象为SELECTION-SCREEN，以三位字符表示(TEXT-001)。
             图标符号:可以在Text Symbols通过@符号来进行引用，如"@01@",可通过程序RSTXICON查看所有的图标
     <5> 屏幕事件处理
       INITIALIZATION. "程序初始化事件，該事件在程序屏幕未顯示之前執行。對程序設置值及屏幕元素進行初始化設置.
       START-OF-SELECTION事件：该事件在单击按钮后触发。
       END-OF-SELECTION事件：该事件应用于所有数据处理完成，即START-OF-SELECTION相关执行事件执行完成。
        但输出屏幕还未显示之前，在实际的应用于一些执行结果的检验等。
       AT SELECTION-SCREEN：选择屏幕显示之后，用来响应回车，F8，F1，F4等事件。
        ...ON <field>：检查具体输入字段(SELECTION-OPTIONS或PARAMETERS)是否完整或正确。
        ...ON VALUE-REQUEST FOR <field low/high>：SELECT-OPTIONS按选择帮助<F4>键时触发该事件。
        ...ON HELP-REQUEST FOR <field low/high>：SELECTION-OPTIONS按选择帮助<F1>键时键发该事件。
        ...ON RADIOBUTTON GROUP <radio>：单选按钮事件，必须进行整体输入检查。
        ...ON BLOCK <block>：框架的触发事件（控制框架中的屏幕元素值的输入）。
        ...OUTPUT：用于屏幕输出时的各屏幕元素值的管控（PBO处理，在选择屏幕显示之前就被调用；响应屏幕上的事件，用户回车或F8后也被调用；通过modify screen可以修改选择屏幕字段）。
        ...ON EXIT-COMMAND：用于"BACK","CANCEL","EXIT"等事件。
     <6>屏幕内创建按钮
      SELECTION-SCREEN PUSHBUTTON [/n] <pos(len)> <name> USER-COMMAND <ucom> [MODIF ID <key>].
        [/n] :按钮初始时距离屏幕左边的位置
        <pos(len)>：PUSHBUTTON按钮在屏幕生成的位置与长度。
        <name>：PUSHBUTTON按钮的名称，给按钮赋值时要用到名字。
        <ucom>：必须指定的字符代码，当用户在选择屏幕上触发按钮时，<ucom>被输入到词典对象字段：
                SSCRFIELDS-UCOMM中，必须显式使用语句TABLES引用SSCRFIELDS。
      实例：  
       TABLES SSCRFIELDS."引用词典对象
       INCLUDE:<icon>.  "按钮中加入图标必须调用该类型库,图标请参考T-CODE：ICON
       SELECTION-SCREEN PUSHBUTTON /1(20) PUBU1 USER-COMMAND ABCD.
       SELECTION-SCREEN SKIP."换行
       SELECTION-SCREEN PUSHBUTTON /10(25) PUBU2 USER-COMMAND ABCE. "位置从10开始
       AT SELECTION-SCREEN OUTPUT.
         MOVE 'CALL NEXT SCREEN' TO PUBU1. "给PUBU1按钮赋值描述
         WRITE ICON_OKAY AS ICON TO PUBU2. "给PUBU2按钮添加图标，并且在给按钮赋值之前，否则将会把文字替换。
         CONCATENATE PUBU2 'My Second Button' INTO PUBU2 SEPARATED BY SPACE. "给第二个按钮添加赋值描述
       AT SELECTION-SCREEN.
        IF SSCRFIELDS-UCOMM = 'ABCD'.
            PERFORM xxxx.  "调用子程序
        ENDIF.
     <7> 在工具栏上新增一个功能按钮
        SELECTION-SCREEN FUNCTION KEY n.
          该按钮的定义保存在系统结构体SSCRFIELDS中，n为一个整数序数最大至5。
        当n等于1时，其按钮描述保存在字段SSCRFIELDS-FUNCTXT_01中，其按钮对象命名为
        "FC01",保存在字段SSCRFIELDS-UCOMM中。
      实例：
         TYPE-POOLS ICON. "Program Icon Library
         TABLES SSCRFIELDS.
         DATA functxt TYPE SMP_DYNTXT. "SMP_DYNTXT(菜单制作器:动态文本的程序接口)
         PARAMETERS: p_carrid TYPE s_carr_id,
                     p_cityfr TYPE s_from_cit.
         SELECTION-SCREEN: FUNCTION KEY 1,
                           FUNCTION KEY 2.
         INITIALIZATION. "屏幕初始化
           functxt-icon_id   = icon_ws_plane.  "文本字段中的图标（替换显示，别名） 
           functxt-quickinfo = 'Preselected Carrier'.  "菜单制作器：信息文本 (4.0)，滑鼠移去过去显示的信息TIP
           functxt-icon_text = 'LH'.  "菜单制作器：图标文本 (4.0)，菜单名称
           sscrfields-functxt_01 = functxt.
           functxt-icon_text = 'UA'.
           sscrfields-functxt_02 = functxt.
         AT SELECTION-SCREEN.
           CASE SSCRFIELDS-UCOMM.
              WHEN 'FC01'.
                p_carrid = 'LH'.
                p_cityfr = 'Frankfurt'.
              WHEN 'FC02'.
                p_carrid = 'UA'.
                p_cityfr = 'Chicago'.
           ENDCASE.
## 字段符号：filed-symbols

## 内表操作 
    【增删改】
      <1> Loop AT循环(sy-tabix:索引号)
         LOOP AT itab {INTO wa}|{ASSIGNING <fs> [CASTING]} | {TRANSPORTING NO FILDS} 
            [[USING KEY key_name] [FROM idx1] [TO idx2] [WHERE log_exp|(cond_syntax)]].
         ENDLOOP.
      <2> INSERT <wa> INTO TABLE <itab>.[单条]
          INSERT <wa> INTO <itab> INDEX <idx>.[索引]
          INSERT LINES OF <itab> [FROM <n1>] [TO <n2>] INTO TABLE <itab>.[批量插入]
          APPEND <wa> TO <itab>.
          APPEND LINES OF <itab1> [FROM<n1>] [TO<n2>] TO <itab2>.
      <3> READ TABLE <itab> WITH KEY {<k1>=<f1>...[BINARY SEARCH]} INTO <wa>}
          [COMPARING <f1><f2>...|ALL FIELDS]  [TRANSPORTING <f1> <f2> ...|ALL FIELDS
                                              |NO FIELDS] | ASSIGNING <fs>.
       COMPARING:根据关键字读取指定的单行与工作区<wa>中的相应组件进行比较。内容相同SY-SUBRC=0
      <4> MODIFY itab1.
          MODIFY itab INDEX idx FROM <wa> [TRANSPORTING <f1> <f2> ...].
          MODIFY TABLE <itab> FROM <wa> [TRANSPORTING <f1> <f2>...].[修改单条]
          MODIFY <itab> FROM <wa> TRANSPORTING <f1> <f2> ... WHERE <cond>.[修改多条]
    
          UPDATE dbtab SET f1=g1 ... fi=gi WHERE <con>.
      <5> DELETE TABLE <itab> FROM <wa> [USING KEY key_name].[删除单条]
          DELETE TABLE <itab> WITH TABLE KEY <k1>=<f1>.. .
          DELETE <itab> WHERE <cond>.[删除多行]
          DELETE <itab> [INDEX <idx>].
          删除重复
      (1)SORT record_tab. //首先进行排序处理
          (2)DELETE ADJACENT DUPLICATES ENTRIES FROM itab [USING KEY key_name] 
                                             [COMPARING K1 K2...] [ALL FIELDS].//删除
      <6> COLLECT <wa> INTO <itab>.
      <7> 第二索引
         UNIQUE KEY.唯一升序第二索引
         NON-UNIQUE KEY.非唯一升序第二索引
         通过第二索引在无法使用主键时，可以加快大量处理数据的速度。
    【查】
      SELECT SINGLE ... INTO [CORRESPONDING FIELDS OF] <wa> WHERE ...
      SELECT ... INTO|APPENDING CORRESPONDING FIELDS OF TABLE <itab>...
      RANG条件内.表：
        RANGES sel FOR obj [OCCURS n].
        SELECT-OPTIONS selcrit FOR {dobj|(name)}.
      FOR ALLENTRIES:
        1:会自动删除重复行   2:WHERE后还有其他条件，会忽略后续条件
      表连接：
        INNER JOIN、LEFT OUTER JOIN.
    【SAP锁】
      通用数据库表锁函数：ENQUEUE_E_TABLE、DEQUEUE_E_TABLE、DEQUEUE_ALL
      特定数据库表函数：ENQUEUE_<LOCK OBJ>、DEQUEUE_<LOCK OBJ>
      自定义锁对象：EZ_/EY_命名
      S:共享锁     E:可重入的排他锁     X:排他锁
      1、在SE11里创建锁对象，自定义的锁对象都必须以EZ或者EY开头来命名。一个锁对象里只包含一个
         PRIMARY TABLE，可以包含若干个SECONDARY TABLE，锁的模式有三种：E，S，X。
       模式E：当更改数据的时候设置为此模式。   (Shared lock, read lock) 【一般使用E】
       模式S：本身不需更改数据，但是希望显示的数据不被别人更改 (Exclusive lock, write lock)
       模式X：和E类似，但是不允许累加，完全独占。 
              (Exclusive lock, extended write lock, cannot be cumulated)
      2、上锁的一般步骤
          先上锁，上锁成功之后，从数据库取数据，然后更改数据，接着更新到数据库，最后解锁。
       按照这个步骤，才能保证更改完全运行在锁的保护机制下。
      3、上锁与解锁
       ENQUEUE_<lock object的名字> 对象 EZZSOPR0032 要求的锁定
       DEQUEUE_<lock object的名字> 释放对象 EZZSOPR0032 的锁定
         有些情况下，程序中设置成功的逻辑锁会隐式的自己解锁。比如说程序结束发生的时候
      （MESSAGE TYPE为A或者X的时候），使用语句LEAVE PROGRAM，LEAVE TO TRANSACTION，或者在
       命令行输入/n回车以后。使用DEQUEUE FUNCTION MODULE来解锁的时候，不会产生EXCEPTION。
       要解开你在程序中创建的所有的逻辑锁，可以用FM：DEQUEUE_ALL.
    【事务处理】
      COMMIT WORK.         异步更新。
      COMMIT WORK AND WAIT.同步跟新，执行结果可通过sy-subrc判断是否提交成功。
      ROLLBACK WORK.
## MESSAGE ：SE91
    <1> 消息类的操作
      使用T-CODE:SE91对Message定义，还能够对Message进行创建，修改及删除等维护操作。
      Message Short Text字段为类描述，可以定义输入参数&，如"1&2&3&"表示有三个输入参数。
      Message共分以下几种类型：E:错误、W:警告、I：信息、A：异常中止、S:成功。
      引用语法为: Message W000(00)，表示调用'00'类的'000' Message类型为警告。
        EX: Message W001(ZTEST) WITH 'P1' 'P2' 'P3'.
    1. 消息ID MESSAGE e001(00) WITH '12345678'. //利用定义的参数
    2. MESSAGE 'XXXXXXXXXX' TYPE 'X'.          //直接附加消息
    3. MESSAGE s001(00) WITH 'No data' DISPLAY LIKE 'E'.
       EXIT.                                   //Screen 界面查询数据无，则返回原界面
## ALV常使用的BAPI
    在ALV开发中有两个重要的对象：LAYOUT和FIELDCAT,它们同属于类型池SLIS。
       LAYOUT主要用于设置ALV的输出格式，如输出字段的颜色、表格中的线条等；
       FIELDCAT主要用于ALV结构定义，包括具体字段的名称、类型、格式等属性.
    常使用的开发类:
       <1> REUSE_ALV_FIENDCATALOG_MERGE：根据内表结构返回FIELDCAT字段结构信息.
       <2> REUSE_ALV_GRID_DISPLAY/REUSE_ALV_LIST_DISPLAY：输出ALV报表，定义其为GRID模式还是LIST模式。参数结构一样。
       <3>
## ALV 报表的表头字段显示设置
    call function 'LVC_FIELDCATALOG_MERGE'
    exporting
      I_BUFFER_ACTIVE        = I_BUFFER_ACTIVE
      I_structure_name       = 'ZALV_FEED' "ALV需要显示的字段结构
      I_CLIENT_NEVER_DISPLAY = 'X'
      I_BYPASSING_BUFFER     = I_BYPASSING_BUFFER
      I_INTERNAL_TABNAME     = I_INTERNAL_TABNAME
        changing
          ct_fieldcat         = lt_fieldcat  "对应ALV显示的字段结构
    exceptions
      inconsistent_interface = 1
      program_error          = 2.
     可以通过loop对相应的ALV字段描述进行自定义设置

## Layout 字段
    DATA wa_alv_field TYPE SLIS_LAYOUT_ALV.
    LAYOUT主要用于设置ALV的输出格式，如输出字段的颜色、表格中的线条等；
      0.COLTAB_fieldname type slis_fieldname: 设置单元格颜色
      1.EDIT：设置ALV是否为可编辑模式。
      2.COLWIDTH_OPTIMIZE：将ALV字段宽度设置为最优化，按实际输出内容宽度自动匹配。
      3.NO_VLINE：输出ALV表格不显示垂直格式。
      4.NO_ULINE_HS：输出ALV表格不显示水平格线。
      5.INFO_FIELDNAME：设置颜色属性。
      6.KEY_HOTSPOT：设置关键字段热点。
      7.NO_COLNAME：是否显示字段名。
      8.ZEBRA：使ALV表格按斑马线间隔条纹方式显示，以便显示效果更有美观。
      9.BOX_FIELDNAME：设置ALV表格是否显示选择按钮字段。
      10.f2code  like   sy-ucomn. gs_layout-f2code='&ETA'[双击时触发的funcode]
      11.INFO_FIELDNAME：用于设置ALV输出报表每一行的颜色，其参数为输出内表的字段名称，
        要注意的是使用该属性需要同时在内表中定义一个与该参数所定义字段名相同的字段，例如：
      LAYOUT-INFO_FIELDNAME = 'COLOR'.倘若其数据输出内表名为LT_OUT,则需要在该内表增加一字段
      “COLOR”，并为其内表每行复制，颜色参数范围C000~C999，例如：LT_OUT-COLOR = 'C012'.
    
    【颜色】
      行颜色:gs_layout-<info_fieldname> = 'COLOR'
      列颜色:gt_fieldcat-emphasize = 'C510'.[1：C固定，2：颜色值0~7,3：高亮0、1(X)，
                                             4：颜色反转，0、1]
      单元格颜色:gs_layout-<coltab_fieldname>='COLORTABLE'.
    【可编辑】
      整体可编辑：gs_layout-edit = 'X' 
      某列可编辑：gt_fieldcat-edit = 'X'
      单元格可编辑：
## 9.Fieldcatalog 字段
    FIELDCAT主要用于ALV结构定义，包括具体字段的名称、类型、格式等属性.
      DATA lt_alv_fieldcat TYPE SLIS_T_FIELDCAT_ALV WITH HEADER LINE.
    　　1.KEY：将定义字段设置为KEY值。
        2.ICON：将定义字段以ICON的形式显示。
        3.CHECKBOX：将定义字段以CHECKBOX的形式显示。
        4.JUST：定义字段对齐方式(R)RIGHT、(L)LEFT、(C)CENTER。
        5.IZERO：将定义字段以前导"0"的形式显示。
        6.NO_SIGN：将定义字段符号设置为不显示。
        7.NO_ZERO：定义字段是否显示。
        8.EMPHASIZE：设置字段的颜色。
        9.DO_SUM：对字段进行汇总。
        10.SELTEXT_L/M/S：设置字段名称描述长/中/短。
        11.DDICTXT：设置字段显示字符串。
        12.HOTSPOT：设置字段是否有热点(热点字段显示有下划线)。
        13.NO_OUT: 隐藏不需要的字段(NO_OUT = 'X')。
        14.EDIT(1) type c: 是否可编辑
        15:COL_POS like sy-cucol: 列输出位置
        16:FIX_COLUMN(1) type c: 列固定不滚动，颜色不会发生变化
        17:convexit : 设置转换规则，对应于Domain中的转换规则	
    
    fieldname  type slis_fieldname    (列显示的设置)
    cfieldname type slis_fieldname	(金额字段所参照的货币单位字段名)
    ctabname   type slis_tabname	  (金额字段所参照的货币单位表名)
    qfieldname type slis_fieldname	(数量字段所参照的货币单位字段名)
    qtabname   type slis_tabname	  (数量字段所参照的货币单位表名)
    
    自定义FIELDCAT字段结构：
      "定义宏来设置FIELDAT属性 &1 &2 &3分别为参数
        DEFINE fieldcatset.
          lt_alv_fieldcat-REF_TABNAME ='LSPFLI'.
          lt_alv_fieldcat-FIELDNAME = &1.
          lt_alv_fieldcat-SELTEXT_L = &2.
          lt_alv_fieldcat-COL_POS = &3.
          APPEND lt_alv_fieldcat.
        END-OF-DEFINITION.
        fieldcatset 'CARRID' '航线承运人' SY-TABIX.
## 10.单元格中的数据被修改后，将ALV单元格中的数据立即刷新到ABAP对应的内表中
    方法一：通过对REUSE_ALV_GRID_DISPLAY函数参数i_grid_settings-edt_cll_cb进行设置：
    i_grid_settings-edt_cll_cb  = 'X' .
    CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING i_grid_settings = i_grid_settings
    方法二：通过函数参数I_CALLBACK_USER_COMMAND指定的回调Form的参数slis_selfield进行设置：
    FORM user_command USING ucomm LIKE sy-ucommselfield selfield TYPE slis_selfield.
        selfield-refresh = 'X'.
      CASE ucomm.
        WHEN 'UPDATE'.
        PERFORM frm_update.
      ENDCASE.
    ENDFORM. 
## 11 自定义按钮使用后刷新ALV
     form frm_user_command using r_ucomm     like sy-ucomm
                            rs_selfield type slis_selfield.
      case r_ucomm.
       when 'PROFIT'.
          perform frm_cycle_count_profit.
       when others.
      endcase.
         rs_selfield-refresh = 'X'.
     endform.                    "frm_user_command
## 12 设置工具导航栏 GUI Status
    GUI Status参数设置共包括3个部分：
      1.菜单栏(Menu Bar)：用于设置主菜单选项。
      2.应用工具条(Application ToolBar)：用于设置应用工具栏按钮，包括按钮名称、按钮描述、及按钮所对的ICON图标。
      3.功能键(Function Key)：为按钮分配功能键代码，包括系统标题按钮(如返回、退出、关闭等)及通过Application ToolBar所定义的客制化按钮。
      4. 对于定义的按钮，我们可以通过系统变量SY-UCOMM来获取它的功能代码。
        AT USER-COMMAND.   "当单击某个按钮时，触发该事件
          CASE sy-ucomm.  "获取所操作按钮的功能代码(FUNCTION Code)
      5.调用显示，应用于START-OF-SELECTION事件
         SET PF-STATUS <STATUS_NAME>.
         不显示某些按钮：SET PF-STATUS <STATUS_NAME> EXCLUDING <extab>.
     6.在ALV函数中使用
      call function 'REUSE_ALV_GRID_DISPLAY_LVC'
        exporting
          i_callback_program       = sy-repid 
          i_callback_pf_status_set = 'FRM_SET_STATUS'
          i_callback_user_command  = 'FRM_USER_COMMAND'
          is_layout_lvc            = lw_layout
          it_fieldcat_lvc          = gt_fieldcat
          i_default                = 'X'
          i_save                   = 'X'
       tables
          t_outtab                 = gt_alv_pi_diffs
       exceptions
          program_error            = 1.
    
      form frm_set_status using extab type slis_t_extab.
         data: ls_slis_extab type slis_extab.
          SET PF-STATUS <STATUS_NAME>.
      endform.                    "frm_set_status
    GUI TITLE设置：
        GUI TITLE 用于定义Report标题栏内容.
      定义：
        Create-->GUI Titles：可以输入&符号作为Title,当程序运行时对其填充动态文本。
      在程序中调用：
        SET TITLEBAR 'TITLE_BAR' WITH SY-DATUM 'IFENER' 'BAR TEST'."设置TITLEBAR，并赋参数列表

## 13. OO ALV
    相关类：
      CL_GUI_CUSTOM_CONTAINER   ：用户自己定义控件区域
      CL_GUI_DOCKING_CONTAINER  : 动态创建容器，不需要在创建时绑定到预先绘制好的自定义控件中
      CL_GUI_SPLITTER_CONTAINER ：可在同一屏幕创建多个ALV显示
      CL_GUI_ALV_GRID           ：ALV控制类
    
      DATA : obj_wcl_container TYPE REF TO cl_gui_custom_container, "控制容器类
             obj_wcl_alv TYPE REF TO cl_gui_alv_grid . "ALV控制类
      DATA GS_LAYOUT1 TYPE LVC_S_LAYO. "布局结构
      DATA GT_FIELDCAT TYPE LVC_T_FCAT. "存放字段目录的内表
      DATA GS_FIELDCAT TYPE LVC_S_FCAT.
      DATA lt_exclude TYPE ui_functions. "alv不需要的图标按钮
    1、控制区域、容器、Grid关系
       先在屏幕绘制一个用户自定义控件区域，然后以自定义区域为基础创建 CL_GUI_CUSTOM_CONTAINER
     容器实例,最后以此容器实例来创建 CL_GUI_ALV_GRID实例。
        DATA : obj_wcl_container TYPE REF TO cl_gui_custom_container, "控制容器类
               obj_wcl_alv TYPE REF TO cl_gui_alv_grid . "ALV控制类
        IF obj_wcl_alv IS INITIAL.
           CREATE OBJECT obj_wcl_container
              EXPORTING
                 container_name  =  'OBJ_WCL_CONTAINER'.
           CREATE OBJECT obj_wcl_alv
              EXPORTING
                 i_parent  = obj_wcl_container.
    2、给ALV对象注册事件
      (1) HANDLE_TOOLBAR:这个事件用于给ALV加自定义工具条按钮。
      (2) HANDLE_CLICK:用于给ALV点击其中一行后处理代码段。
      (3) HANDLE_COMMAND:事件用于接收用户按了自定义按钮后，触发的代码段。
      (4) HANDLE_DOUBLE_CLICK：双击事件
      (5) 
      事件的使用：
        <1> 定义事件方法
        <2> 指定事件的执行方法代码
        <3> 事件变量实例化
        <4> 把事件指定到ALV控制中(注册事件)
    
        CREATE OBJECT event_receiver.
        SET HANDLER event_receiver -> handle_double_click FOR obj_wcl_alv.
    3、CL_GUI_ALV_GRID重要方法
      *-----显示ALV 
         CALL METHOD obj_wcl_alv->set_table_for_first_display 
            EXPORTING 
               is_variant                    = ls_variant   :指定布局变式
               is_layout                     = ls_layout 
               i_save                        = 'A'          :保存表格布局
               it_toolbar_excluding          = lt_exclude 
            CHANGING 
               it_outtab                     = gt_list[]    :需要显示的内表数据
               it_fieldcatalog               = lt_fieldcat  :结构字段
            EXCEPTIONS 
               invalid_parameter_combination = 1 
               program_error                 = 2 
               too_many_lines                = 3 
               OTHERS                        = 4.
    
       ENDFORM.                    " FRM_ALV_DISPLAY
    
      XXX -> REFRESH_TABLE_DISPLAY.
        IS_STABLE : 刷新的稳定性，就是滚动条保持不动
        I_SOFT_REFRESH:  软刷新，临时给ALV创建的合计，排序，等保持不变

## 14.前导零问题
    加上p_in的前导零
      FORM add_zero  CHANGING p_in.
    CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
      EXPORTING
        input  = p_in
        IMPORTING
          output = p_in.
     ENDFORM.
    去除p_out的前导零
      FORM del_zero CHANGING p_out.
        CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
        EXPORTING
          input  = p_out
        IMPORTING
         output = p_out.
## 15.Hash table, sort table, standy table
       对于排序内表，不要使用append，append lines附加数据，使用insert、insert lines向
    排序内表中插入数据
## 16.search help 的创建
    PARAMETERS p_cur TYPE XXX VALUE CHECK .
    PROCESS ON VALUE-REQUEST ， AT SELECTION-SCREEN ON VALUE-REQUEST。
    在屏幕的 ON VALUE-REQUEST 事件里可以通过下面几个函数来创建搜索帮助：
      F4IF_ FIELD _VALUE_REQUEST ： 函数的作用是在运行时，可以动态的为某个屏幕字段指定 
         Search Help ，这个被引用的 Help 来自某个表（或结构）字段上绑定的 Help
      F4IF_ INT_TABLE _VALUE_REQUEST : 在程序运行时， 将某个内表动态的用作 Search help 的
         数据来源,即使用该函数可以将某个内表转换为 Search help ，可实现联动效果
      TR_F4_HELP ： 简单实现 Search Help ，数据来源于内表。
## 17. SQL 语句的执行顺序
    书写顺序：SELECT[DISTINCT]-->FROM-->WHERE-->GROUP BY-->HAVING-->UNION-->ORDER BY
    其执行顺序为：FROM-->WHERE-->GROUP BY-->HAVING-->SELECT-->DISTINCT-->UNION->ORDER BY
    1、FROM 才是 SQL 语句执行的第一步，并非 SELECT 
    2、SELECT 是在大部分语句执行了之后才执行的，严格的说是在 FROM 和 GROUP BY 之后执行的。
    3、无论在语法上还是在执行顺序上， UNION 总是排在在 ORDER BY 之前。

# Open SQL 
----------
## Focus
    1. 多表的结合的查询，首先进行各自的关键字查询，然后进行表与表转换可以提升效率
    2. 表相应字段非关键字时，寻找有该关键字的相对应的关联表进行查询并合并表数据
    3. binary search 和 sort 进行使用时注意sort的关键字与binary search 保持统一
    4. MODIFY itab1 INDEX sy-tabix.进行修改时最好定义变量记录sy-tabix.
    5. inner join 和 left outer join 的连接条件最好都是关键字.
    6. 双层loop循环时，第二个内部表定义为SORTED TABLE会大大的提高处理速度

