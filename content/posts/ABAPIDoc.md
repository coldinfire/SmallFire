---
title: "IDoc操作"
date: 2018-10-22T15:15:42+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - IDoc

---


### IDoc

IDoc:是基于文档，用作异步传输数据的载体，类似于XML

● IDoc支持异步、同步、可以收集一定数量的包后发送，最重要的是，IDOC有完整的监控系统和错误处理机制。

● IDoc支持SAP系统集团之间，SAP-CRM/SRM/PI等之间，SAP-第三方系统之间的集成

● 通过系统预定义IDOC类型，可以自动收集IDoc，挂JOB定时发送，也可以配置消息控制。

数据段是IDoc结构组件，是IDoc构成的单元;有Segment type<数据段类型>与Segm.definition<数据段定义>

- :Segment type名称与SAP版本无关，以"E1/Z1"开头

- :Segment definition名称与SAP版本有关，外部系统确定数据段版本的关键；以"E2/Z2"开头+版本号
  访问IDoc中具体某个字段时，需要通过Segment type。

2.IDoc发送接收流程
                              

```JS
    出站配置   					   入站配置
WE31:开发段类型                              WE31:开发Segment Type
WE30:开发IDOC基本类型                        WE81：开发Message Type
WE82:将基本类型绑定到消息类型                 WE82:Message Type和IDoc Type绑定
BD64:添加视图模型，添加消息类型配置伙伴参数    BD64:增加消息类型
WE20:配置发送系统出站信息                     WE20:配置接收系统入站信息
SE38:编写发送程序                            SE37:编写接收接口
WE14:若为黄灯，手动发送                       WE57:分配IDOC类型给处理函数
BD51:配置进站函数模块属性
WE42:配置进站处理代码
WE02:IDOC发送信息检查
```

3.IDoc常用TCode

/BD87

/SM58

/WE09

/WE18

/WE19

/WE20

/SUIM

/GS03

/BDLS