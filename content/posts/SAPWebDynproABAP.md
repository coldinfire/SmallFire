---

title: " 创建一个Web Dynpro ABAP程序实例 "
date: 2018-10-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---



- 在ERP系统中找到Report对应的程序，在Web Dynpro NID系统中创建对应的结构，分别对应选择屏幕和对应的ALV报表。

### SE80创建WDA

输入WDA的Name和View Name，创建Web Dynpro并激活

![Web Dynpro 创建](/images/webdynproABAP/1.1.png)

![Web Dynpro 创建](/images/webdynproABAP/1.png)

双击对象名ZPR_ZPEFF，然后添加Component Use

![Web Dynpro 添加 Component Use](/images/webdynproABAP/2.png)

然后双击COMPONENTCONTROLLER,在Properties中创建Components

![添加Components](/images/webdynproABAP/3.png)

切换页签到Context(还是COMPONENTCONTROLLER),创建节点.

进到创建Nodes的界面,输入Node Name, Dictionary structure,然后Cardinality改成0..n，然然后点击Add attribute from structure来选择需要的字段

![Create node](/images/webdynproABAP/4.1.png)

![Add Attribute from Structure](/images/webdynproABAP/5.png)

![Result](/images/webdynproABAP/6.png)

再建一个Node (ZOPTION)用来放复选框

![Create Check box node](/images/webdynproABAP/7.png)

在ZOPTION下创建二个属性，对应P_TOTAL和P_DETAIL

![Add attribute](/images/webdynproABAP/8.png)

切换到页签 Attribute，然后维护以下三个属性

![Add attribute](/images/webdynproABAP/9.png)

在页签Methods我们用到2个方法，(具体代码后面会给)：

- DATA_LOAD是手动添加的方法

- WDDOINIT是系统自动添加的方法

![Add METHODS](/images/webdynproABAP/10.png)

### View的处理

#### INPUT_VIEW处理

首先进到INPUT_VIEW中的Context页签中,然后将右边的Node拖到左边,如下图：

![Input View Context](/images/webdynproABAP/11.png)

然后在Inbound Plugs维护Plug
Name : FROM_ALV_VIEW

![Input View Inbound Plugs](/images/webdynproABAP/12.png)

然后在Outbound Plugs维护Plug
Name : TO_ALV_VIEW

![Input View Outbound Plugs](/images/webdynproABAP/13.png)

**接下来我们在Layout设置选择界面的信息。**

首先设置一个执行按钮，右键ROOTUIELEMENTCONTAINER,选择Insert Element,然后输入ID为TOOLBAR1,Type为TOOLBAR点击确定。

![Input View Layout](/images/webdynproABAP/14.png)

右键TOOLBAR1,选择Insert Toolbar Element,输入ID为EXCUTE, Type为TOOLBAR_BUTTON.

![Input View Layout](/images/webdynproABAP/15.png)

![Input View Layout Ele](/images/webdynproABAP/16.png)

然后设置EXCUTE的属性，输入图标名称，创建一个Event,如下图所示

![Input View Layout Button](/images/webdynproABAP/17.png)

然后双击EXCUTE,写入逻辑,当点击按钮时，跳入到ALV_VIEW视图。

```JS
DATA lo_componentcontroller TYPE REF TO ig_componentcontroller .
    lo_componentcontroller =   wd_this->get_componentcontroller_ctr( ).
    """"来源ComponentController中method
    lo_componentcontroller->data_load(
    ).
    wd_this->fire_to_alv_view_plg(
    ).
```

![Input View Event](/images/webdynproABAP/18.png)

然后我们继续回到Layout,接下来创建Group，右键ROOTUIELEMENTCONTAINER,然后输入ID为SELECTIONS1,选择Type为GROUP，如下图：

![Input View Layout Group](/images/webdynproABAP/19.png)

然后修改Group属性，Layout选择MatrixLayout ,然后width维护100%

![Input View Layout property](/images/webdynproABAP/20.png)

再然后右键SELECTIONS1,选择Insert Element,输入ID和Type

![Input View Layout selection](/images/webdynproABAP/22.png)

接下来我们以同样的方式创建GROUP，名称为SELECTIONS2

右键SELECTIONS2,然后选择Create
Container Form,选择字段，确定。

![Input View Layout ](/images/webdynproABAP/21.png)

这里我们回到COMPONENTCONTROLLER的Methods,将用到的2个方法写入逻辑。

- **WDDOINIT** – 初始化选择界面

- **DATA_LOAD** – 加载数据

#### ALV_VIEW处理

将二个ALV报表要显示的结果的结构添加到Component
Controller中的上下文中;

右键Views然后创建新的VIEW用来展示ALV报表 ：ALV_VIEW

添加Context 和Properties/Component Use

![ALV View Context ](/images/webdynproABAP/23.png)

![ALV View Properties ](/images/webdynproABAP/23.1.png)

然后Inbound Plugs和Outbound
Plugs分别维护 

- FROM_INPUT_VIEW : Handle From_input_view
- TO_INPUT_VIEW : Handle To_input_view

然后切换到页签Layout,新建一个返回按钮,BACK,然后双击BACK,写入返回逻辑。

![ALV View Layout back ](/images/webdynproABAP/26.png)

![ALV View Layout back ](/images/webdynproABAP/27.png)

我们再回到Layout,创建一个HORIZONTAL_GUTTER类型是HORIZONTAL GUTTER将按钮和ALV输出数据隔开。

![View Layout ](/images/webdynproABAP/24.png)

然后创建一个DISPLAY_DATA 类型是`ViewContainerUIElement`。

![View Layout ](/images/webdynproABAP/25.png)

再切换到Methods中，在WDDOINIT中写实现ALV逻辑。

```JS

```



### Windows 处理

打开Windows并双击视图,然后将ALV_VIEW拖到ZWDA_DEMO下面，如图：

![Windows Add](/images/webdynproABAP/28.png)

右键INPUT_VIW中的SELECT_OPTIONS,将select_options绑定

![Windows Input View Select Options](/images/webdynproABAP/29.1.png)

![Windows Input View Select Options Embed](/images/webdynproABAP/29.2.png)

然后右键INPUT_VIEW下的TO_ALV_VIEW, 选择 Create
Navigation Link

![Windows Input View OUT](/images/webdynproABAP/29.3.png)

![Windows Input View OUT](/images/webdynproABAP/29.4.png)

再右键ALV_VIEW下的DISPLAY_DATA,进行绑定

![Windows Input View OUT](/images/webdynproABAP/30.png)

然后右键ALV_VIEW中的TO_INPUT_VIEW, Create Navigation Link

![Windows Input View OUT](/images/webdynproABAP/31.png)

最后效果如下图所示： 

![Windows Result](/images/webdynproABAP/35.png)

### 创建Web Dynpro Component(Interface)

右键Object Name -> Create -> Web Dynpro Application

![Windows Result](/images/webdynproABAP/32.png)

可以在浏览器通过产生的URL进行测试

![Windows Result](/images/webdynproABAP/33.png)



```JS
method WDDOINIT .
TYPES:BEGIN OF soption,
        typename TYPE string,
        id       TYPE string,
      END OF soption.
  DATA:l_alv_cmp_usage     TYPE REF TO if_wd_component_usage,
       l_alvctrl           TYPE REF TO iwci_salv_wd_table,
       l_alvmodel          TYPE REF TO cl_salv_wd_config_table,
       l_it_range_table    TYPE REF TO data,
       l_read_only         TYPE abap_bool,
       l_soption_cmp_usage TYPE REF TO if_wd_component_usage,
       l_alvcolset         TYPE REF TO if_salv_wd_column_settings,
       l_column            TYPE REF TO cl_salv_wd_column,
       l_it_field          TYPE TABLE OF dfies,
       l_wa_field          TYPE dfies,
       l_fieldname         TYPE string,
       l_invisible         TYPE c,
       l_position          TYPE i,
       l_it_soption        TYPE TABLE OF soption,
       l_wa_soption        TYPE soption,
       lo_select_options   TYPE REF TO iwci_wdr_select_options,

       l_it_range_tableh    TYPE REF TO data,
       l_soption_cmp_usageh TYPE REF TO if_wd_component_usage,
       l_col_head           TYPE REF TO cl_salv_wd_column_header ,
       l_it_soptionh        TYPE TABLE OF soption,
       l_wa_soptionh        TYPE soption,
       lo_select_optionsh   TYPE REF TO iwci_wdr_select_options.
  DATA: l_abap_bool        TYPE abap_bool.

  l_alv_cmp_usage = wd_this->wd_cpuse_alv( ).
  IF l_alv_cmp_usage->has_active_component( ) IS INITIAL.
    l_alv_cmp_usage->create_component( ).
  ENDIF.

* Get reference to model 获取参考模型
  l_alvctrl = wd_this->wd_cpifc_alv( ).
*
  DATA  lo_nd_zoption TYPE REF TO if_wd_context_node.
  DATA: lo_el_zoption TYPE REF TO if_wd_context_element,
        ls_zoption    TYPE wd_this->element_zoption ,
        lv_p_total  TYPE wd_this->element_zoption-p_total,
        lv_p_detail  TYPE wd_this->element_zoption-p_detail.

  lo_nd_zoption   = wd_context->get_child_node( name = wd_this->wdctx_zoption ).
  lo_el_zoption = lo_nd_zoption->get_element( ).

  """"选择界面复选框设置默认值
  IF lo_el_zoption IS NOT INITIAL.
  lo_el_zoption->set_attribute(
    EXPORTING
      name =  `P_DETAIL`
      value = 'X' ).
  ENDIF.
* create the used component
  l_soption_cmp_usage = wd_this->wd_cpuse_select_options( ).

  IF l_soption_cmp_usage->has_active_component( ) IS INITIAL.
    l_soption_cmp_usage->create_component( ).
  ENDIF.
  lo_select_options = wd_this->wd_cpifc_select_options( ).
* init the select screen
  wd_this->m_handler = lo_select_options->init_selection_screen( ).
  wd_this->m_handler->set_global_options(
                              i_display_btn_cancel  = abap_false
                              i_display_btn_check   = abap_false
                              i_display_btn_reset   = abap_false
                              i_display_btn_execute = abap_false ).
*
  l_wa_soption-typename = 'ARBPL'.
  l_wa_soption-id       = 'ARBPL'.
  APPEND l_wa_soption TO l_it_soption.

  l_wa_soption-typename = 'KOSTL'.
  l_wa_soption-id       = 'KOSTL'.
  APPEND l_wa_soption TO l_it_soption.

  l_wa_soption-typename = 'AUFNR'.
  l_wa_soption-id       = 'AUFNR'.
  APPEND l_wa_soption TO l_it_soption.

  l_wa_soption-typename = 'AUFART'.
  l_wa_soption-id       = 'AUART'.
  APPEND l_wa_soption TO l_it_soption.

    l_wa_soption-typename = 'BUDAT'.
  l_wa_soption-id       = 'BUDAT'.
  APPEND l_wa_soption TO l_it_soption.

*  DATA: lt_werks_table TYPE RANGE OF werks_d .
*  DATA: ls_werks_table LIKE LINE OF lt_werks_table .
*  FIELD-SYMBOLS: <it_werks_table> LIKE lt_werks_table .
*
  LOOP AT l_it_soption INTO l_wa_soption.
    l_it_range_table = wd_this->m_handler->create_range_table( i_typename = l_wa_soption-typename ).
*
*    """"选择界面ranges给初始值
*    IF l_wa_soption-typename = 'ZWERKS_D'.
*      ASSIGN l_it_range_table->* TO <it_werks_table> .
*      ls_werks_table-sign   = 'I' .
*      ls_werks_table-option = 'EQ' .
*      ls_werks_table-low    = '1020' .
*      APPEND ls_werks_table TO <it_werks_table> .
*    ENDIF.
*
    wd_this->m_handler->add_selection_field( i_id  = l_wa_soption-id
                                     i_obligatory  = ''
                                        it_result  = l_it_range_table
                                      i_read_only  = l_read_only ).
  ENDLOOP.

* create the used component
  l_soption_cmp_usageh = wd_this->wd_cpuse_select_options( ).
  IF l_soption_cmp_usageh->has_active_component( ) IS INITIAL.
    l_soption_cmp_usageh->create_component( ).
  ENDIF.
  lo_select_optionsh = wd_this->wd_cpifc_select_options( ).
* init the select screen
  wd_this->m_handlerh = lo_select_optionsh->init_selection_screen( ).
  wd_this->m_handlerh->set_global_options(
                              i_display_btn_cancel  = abap_false
                              i_display_btn_check   = abap_false
                              i_display_btn_reset   = abap_false
                              i_display_btn_execute = abap_false ).

  LOOP AT l_it_soptionh INTO l_wa_soptionh.

    l_it_range_tableh = wd_this->m_handlerh->create_range_table( i_typename = l_wa_soptionh-typename ).

    wd_this->m_handlerh->add_selection_field( i_id  = l_wa_soptionh-id
                                          it_result = l_it_range_tableh
                                        i_read_only = l_read_only ).
  ENDLOOP.
endmethod.
```

