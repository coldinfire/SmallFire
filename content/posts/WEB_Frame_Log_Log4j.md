---
title: " 日志使用 "
date: 2018-01-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Log

---

1.log4j 是Apache为java提供日志管理的工具
        作用：可以调试程序，就像输出一样

2.核心概念：
    log4j有三大组件：  日志器（Logger）   日志输出的目标（Appender）  格式化器（Layout）

  日志器（Logger）:用来输出消息的类，可以输出不同级别的，比如错误消息，警告消息等
  日志输出的目标（Appender）：日志输出到哪里去，文件    ，控制台 
  格式化器（Layout）：对输出消息进行格式化，比如添加日期

 3.  建立日志器
     Logger log=Logger.getLogger(Test.class);  
           log.info();
           log.debug();
           

 4.日志级别：从高到低： 

        FATAL:  重大错误（系统有问题）
         ERROR:   错误   （一个模块有问题）
         WARN:    警告（）
         INFO:  信息：可以查看程序执行的流程
         DEBUG:  调试，用来调试程序的bug及显示

  5，log4j.properties

  6.在配置文件里，需要为log4j.properties配置一个根日志器，
  log4j.rootLogger=DEBUG,AA
  log4j.rootLogger=WARN
  log4j.APPENDER.AA=org.apache.log4j.ConsoleAppender

  Appender:
  是用来指定输出目标的类，可以叫发送器
     ConsoleAppender：  向控制台输出日志；
     FileAppender:     向文件输出日志
      DailyRollingFileApperder:

     log4j.appender.AA.File=log.txt
     log4j.appender.AA.LAYOUT=org.apache.log4j.SimpleLayout
     log4j.appender.AA.DatePatten='yyyy-MM-dd'
 layout种类：
 %m:  信息本身
 %c:日志器的名称
 %d:日期，还可以指定日期格式   %d{yyyy-MM-dd HH:mm:ss}
 %p:日志级别
 %n:换行
 %t:当前线程
 %l:输出日志java类的相关信息