---

title: "SAP MM01 屏幕增强"
date: 2019-05-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

[https://blogs.sap.com/2015/03/06/additional-fields-on-the-material-master/](https://blogs.sap.com/2015/03/06/additional-fields-on-the-material-master/)



​	MM01中的Screen完全是通过一个个的subscreen，像一个个的component组合而成的。在原有的subscreen不在customer namespace的情况下，如果不想做Modification，只能通过创建一个自己的subscreen和screen sequence，并把这个sequence放到screen的determination procedure中的方法去做。

#### 一、扩展主数据表

- 1.SE11 选择需要增加字段的物料主数据表(MARA,MARC......)，添加附加结构(ZZ_MARC)

![Create Append structure](/images/MM/MM_Enhance1.png)

- 2.在结构中添加需要的字段内容

![Add fields](/images/MM/MM_Enhance2.png)

- 3.激活附加结构

![Append fields](/images/MM/MM_Enhance3.png)

#### 二、创建自定义的子屏幕

- 1.SPRO > 后勤-常规 >物料主数据 >配置物料主记录>创建定制子屏幕程序(ZMMARC:**SAPL**ZMMARC) 
  - OMT3C：Create Function Group

​		![Configure Path](/images/MM/MM_Enhance4.png)

​		![Create FM Group](/images/MM/MM_Enhance5.png)

- 2.SE80查看Function group `MGD1`选择需要增强字段的子屏幕

  ![MGD1](/images/MM/MM_Enhance6.png)

- 3.从MGD1复制想要增强字段的子屏幕到刚刚创建的FM ,保持屏幕号码一致

  ![Copy Screen](/images/MM/MM_Enhance7.png)

- 4.使用Screen Painter删除复制过来的内容，按实际需求进行屏幕设计，保存并激活

  ![Screen Design](/images/MM/MM_Enhance8.png)

- 5.在Flow logic中注释掉PAI中的内容，然后添加一些必要的逻辑在PBO和PAI中.但是要保留 MODULE GET_DATEN_SUB和SET_DATEN_SUB,这两个Module从数据库读取数据，并将修改后的数据写到系统标准表中

  ![Flow Logic](/images/MM/MM_Enhance9.png)

#### 三、添加子屏幕到标准的视图

- 1.(Tcode OMT3B)：SPRO IMG -> LogisticsGeneral -> Material Master -> Configuring the Material Master-> Define Structure of Data Screens for Each Screen Sequence

  - 可以通过复制现有的屏幕创建自定义的屏幕序列

  - 也可以修改系统定义好的屏幕序列

    ![Screen seq](/images/MM/MM_Enhance10.png)

    ![Data Screen](/images/MM/MM_Enhance11.png)

    ![Subscreens](/images/MM/MM_Enhance12.png)

- 单击 “ *添加新条目”*按钮，并将程序名称替换为自定义的程序名（Function Group 的主程序），然后将自己的屏幕编号（2497）填入到Subscreen Number中,保存

  ![Subscreens](/images/MM/MM_Enhance13.png)

- 此时可以使用MM02,MM03去进行验证

#### 四、在 PAI 中修改新字段数据

​	如果需要处理或修改新字段的数据（以及在 MM01 / MM02 / MM03 中的 PAI 期间的标准字段数据），必须实现客户退出**EXIT_SAPLMGMU_001**。每次触发 PAI 都会调用此用户出口。

​	如果仅需要在 SAVE 操作期间进行一些错误检查，则可以将代码括在诸如

```JS
1. IF sy-ucomm = 'BU' OR SY-UCOMM = 'YES'. "此代码仅在 SAVE 期间执行
2.   " 代码逻辑
3. ENDIF。
```





