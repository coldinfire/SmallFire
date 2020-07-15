---
title: "ABAP 工作流"
date: 2018-11-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - workflow

---


### SAP工作流介绍
   工作流是一个基于某组程序规则所采用的路径。是自动运作的任务的进程中，参与的人、文件、信息或任务，以及各个要素间的传递按照规程运转。它们可能非常简单，如批准或拒绝；或则非常复杂，根据许多部门所涉及的采购订单的发布条件获得许多级别的审批。

   工作流特别适合于多次重复执行类型的业务工作流程。还可以用来处理业务流程中出现的错误和例外：预先在工作流中定义例外事件，当系统自动检查发现例外时，就会有触发某种反应或措施，如给相关人员发Mail等。

### 包含组件 ###

Organizational Plan：管理报告和消息流的层次结构

- Organizational Unit：企业中的功能单元
- Position：代表一个Post
- Job：企业中的功能分类
- Staff Assignments / Assignment of User-id's 

Workflow Builder(SWDD)：创建，显示和更改工作流。提供一个工作流定义视图。

Workflow instance：是工作流的单次运行。 

Task：是由软件自动或由人员执行的过程中的步骤，Activity的描述。 

Work Item：是作为单个工作流步骤执行的任务实例。 

Workflow Container：是收集工作流中使用的所有数据的地方。 

Binding：是一组规则，用于定义将哪些数据传递到进程的哪个部分。 

Event：Triggering Events，Terminating Events。

Agent resolution：流程的节点负责人。

### 定义和创建 ###

#### 定义: ####
   每个workflow都能在SAP中找到业务流程；由很多步骤组成；可以由事件触发。

   模板：SAP提供了大量的Workflow的模板可供参考，如果不符合具体的业务流程，可以对该模板做增强。不过就像SAP标准程序一样，不能对其进行修改，可以根据需求把这个模板复制出来然后对其修改。

   Workflow助手：Business Workplace(TCode：SBWP)

   当Workflow执行到某一步需要特定的用户确认或者批准的时候，就会发出work item到该用户的workplace，以使该用户做出相应的操作。

#### 创建步骤： ####
<一>创建Workflow模板（TCode：PFTC_INS）,  TCode：SWDD——创建工作流
![SWDD](/images/WorkFlow/SWDD.png)

 - Information Area:信息是SAP自动生成的
 - Steps:当前Workflow所使用的Steps的列表
 - Step Types：Steps list （可选不同的组件） 
 - Graphical Model：进行Workflow的流程定义

<二>定义Condition和创建业务所需要的Steps
![Container & Steps](/images/WorkFlow/Container.png)

 - Workflow Container:定义workflow所需要的数据元素；数据元素可定义参考类型，参数设置，初始值。
 - Steps创建：在对应的分支线上根据业务流程创建具体的Step，每个Step都有具体的使用要求。

<三>对Steps进行详细的内容设定（Activity —>Task）
![Steps Details](/images/WorkFlow/StepDetail.png)
      
 - Task：定义系统流程执行事件。
 - Binding：将Workflow定义数据与Task使用字段进行绑定
 - Task Tcode : PFTC_INS / _CHG / _DIS / _COP：Create / Change / Display /Copy Tasks

####  Agents  ####

Role:"assign agents",通过PFCG进行分配到用户    
Rule:"define agents",通过PFAC进行定义

   `PFAC_INS / _CHG / _DIS/_COP：Create / Change / Display /Copy Rules`

Rule:允许我们定义和实施问题处理流程。允许我们根据定义时定义的模板在运行时指定数据。与电子邮件通知一起，工作流程规则可帮助我们自动跟踪和管理问题。如果在工作流中使用Rule来确定责任代理，则Rule解析的结果会存储在容器元素_RULE_RESULT中，可以通过绑定传输到工作流容器中。

Scenario:创建一个rule找到任意user/agent的上级。

Design:
```JS
1.创建一张自定义表ZUsers，存放用户和上级的关联，及用户信息，上级信息。
2.创建一个Function,返回包含user/agent对应的上级的有效信息。ZFIND_SUPERIOR
   自定义的Function应和SAP标准的Module有相同的表参数：
     ACTOR_TAB              STRUCTURE       SWHACTOR
     AC_CONTAINER        STRUCTURE      SWCONT
     lwa_hoders-otype = 'US'.
     lwa_holders-objid = superior.
3.创建rule:ZRULE_TEST.
     Rule Definition：
        Category:选择Agent Determination:Function to be Executed
           Function Module : ZFIND_SUPERIOR.（定义的Function module）
        Container:
           定义参数，参数在Function module中使用。
4.Rule Resolution Based on Evaluation Paths
        维护Evaluation paths: OOAW
   Rule Definition：
        Category:中选择Agent Determination:Function to be Executed
        Function Module:RH_GET_STRUCTURE,激活Evaluation Path
        Evaluation Path:输入评估路径值
   注意：基于评估路径的代理确定Rule的Rule container必须仅包含要为其应用评估路径的组织管理对象。
	 在运行时，系统填充使用绑定从工作流或任务容器中规则容器。
    Container:
         OType  type  OBJEC-OTYPE : Type of the Org Management Object
         ObjID  type  OBJEC-REALO : ID of the Org Management Object
         Org_Agent  type  WFSYST-AGENT : Org Management object
   注意：Container可以传输组织管理对象的元素。在运行时，必须填充容器元素OType和ObjID或容器元素Org_Agent。
		如果填充所有容器元素，仅评估在Org_Agent中传输的值。
```
![Task Details](/images/WorkFlow/TaskDetails.png)

- Object method：定义要调用的Class,Type,Method,并进行字段绑定。
- Object Type：需要定义Interfaces实现IF_WORKFLOW.
- Synchronous object method：同步对象的方法。
- Execution：执行方式的选择

激活保存，运行并查看Log
![执行](/images/WorkFlow/Execute.png)
![Log查看](/images/WorkFlow/Log.png)

 - 可以通过Print Log（Ctrl + P）查看Workflow Classical Technical Log

![Print Log](/images/WorkFlow/PrintLog.png)
![Workflow Log](/images/WorkFlow/WorkFlowLog.png)

- Various Status of Work Item.

![Various Status](/images/WorkFlow/VariousStatus.png)

<四>在程序中调用Workflow
![Function](/images/WorkFlow/Function.png)

- 通过Function(SWU_START_WORKFLOW)调用，根据传入的参数和数据调用对应的Workflow。

<五>通过Event触发Workflow
- SWE2:定义事件    

![SWE2](/images/WorkFlow/SWE2.png)
![SWDD](/images/WorkFlow/SWDD2.png)

```JS
CALL FUNCTION 'SAP_WAPI_CREATE_EVENT'
  EXPORTING
    object_type= 'ZMINORDER'
    object_key= l_obj_key
    event                   = 'APP'
    *commit_work             = 'X'
    event_language= sy-langu
    language                = sy-langu
    user= sy-uname
  TABLES
    *INPUT_CONTAINER         =
    message_lines= l_message_lines
    *MESSAGE_STRUCT          =  .
```


## TCode ##
### 最常用事务码：
```JS
SWDM：Business Workflow Explorer
SWDD：Workflow Builder
SWO1：Business Object Builder
SWETPYV：Display and maintain event type link age
SBWP：Business Workplace
SWI1：Selection report for work items
SWEL：Display Event Trace
SWE2：Create Event
PPOMW：Maintain organizational plan
PFTC_INS / _CHG / _DIS / _COP：Create / Change / Display /Copy Tasks
PFAC_INS / _CHG / _DIS/_COP：Create / Change / Display /Copy Rules
```
分析工具事务码： 
```JS
SBWP：Business Workplace ( Outbox )
SWIA：Process Work Item As Administrator
SWI6：Workflows for Object
SWI14：Workflows for Object Type
SWEL Display event trace
SWI1 Selection report for work items
SWU7 Consistency check for workflow templates
SWU9 Display workflow trace
SWUD Diagnostic tools
SWU3 Customizing
```
