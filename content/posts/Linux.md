---
title: " Linux基础 "
date: 2017-10-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux

---

## 主要内容 ##

- Linux目录组成结构及文件的增删改查，用户，权限管理

- Linux软件包管理、磁盘管理

- Linux网络基础

- Linux状态监控命令

- Linux系统进程管理

- 网络服务

## 目录结构 ##

 -  /：根目录，整个文件系统层次结构的根目录

 -  /bin：需要在单用户模式可用的必要命令

 -  /boot：系统引导程序文件

 -  /dev：必要设备,磁盘等必须的硬件设备

 -  /etc：配置文件   /etc/xml:xml配置文件  、/etc/opt:/opt/配置文件

 -  /home：用户的家目录，包含保存的文件、个人设置等，一般为单独的分区

 -  /lib：/bin/ and /sbin/中二进制文件必要的库文件

 -  /opt：可选应用软件包

 -  /root：超级用户的家目录

 -  /sbin：必要的系统二进制文件，普通用户执行不了该目录命令

 -  /srv：站点的具体数据，由系统提供

 -  /tmp：临时文件，在系统重启时目录中文件不会被保留

 -  /usr：默认软件都会存于该目录下。用于存储只读用户数据的第二层次；包含绝大多数应用程序

 -  /var：在正常运行的系统中其内容不断变化的文件

## 命令解析 ##
**Linux命令区分大小写**

查找命令：

   ♦ type：显示有关命令类型的信息

   ♦ which：找到命令在哪个文件下

   ♦ help -m [command]：显示shell内置的参考页面   

   ♦ man [command]：显示在线命令参考

命令的语法格式：cmd 【options】【arguments】

  - 命令：告诉Linux要做什么

  - 选项：说明运行方式

  - 参数：说明影响操作

Ctrl + a：跳到输入命令的头部

Ctrl + e：跳到输入命令的尾部

可执行命令分类：

   ♦ 内置命令：在shell内部系统定义

   ♦ 外置命令：存放在/bin、/sbin目录下的命令

   ♦ 实用命令：放在/usr/bin,/usrlocal/bin等目录下的实用程序

   ♦ 用户程序：用户的程序经过编译生产可执行文件后，可作为Shell执行

   ♦ Shell脚本：由Shell语言编写的批处理文件，可作为Shell命令运行

通配符：与其他语言表达式类似

   ♦ *：匹配任何字符和任何数目的字符

   ♦ ？：匹配单一数目的任何字符

   ♦ [ ]：匹配[ ]之内的任意一个字符

   ♦ [! ]：匹配除了[! ]之外的任意一个字符，!表示非的意思

## 系统工具

### 磁盘分析 ###
  - df [-ah]：系统磁盘使用信息
  - du [-ah]：显示当前磁盘文件的大小
  - free：显示内存剩余大小
  - fdisk [-l] [/dev/sda]:磁盘分区表操作工具
  - fdisk  /dev/sda：设置分区
  - partprobe：更新分区表
  - mkfs [tab tab]：查看mkfs支持文件格式
  - mkfs [-t 文件格式] 装置文件名：磁盘格式化 
      - mkfs.ex4 /dev/sda{1..3}：将该磁盘格式为ex4格式
  - fsck :磁盘检验
  - mount【装置文件名】【挂载点】：磁盘挂载
      - mount /dev/sdb1  /sdb1/
  - umount [-ln]：卸载  
      - [-l]：强制卸除 
      - [-n]：不升级/etc/mtab下卸除
- mount -o remount,ro /dev/sdb3：修改权限

### 网络性能
  - ifconfig [card] [x]：网络状态  
       - [-a]：显示全部接口信息 
       - [up/down]：开启关闭网卡 
       - etho:第一块网卡   lo:回环地址
  - ps [-aux]：显示系统所有在运行的进程
       - 配合 `grep`实现查找特定的进程
  - pstree：以树形显示进程
  - top：动态进程
  - free：查看内存的使用状况 [-h]：以G为单位显示
  - kill [-9] pid：杀死对应的pid进程  [-9]：强制杀死
  - [command] &：后台运行该程序
  - jobs：列出后台程序的一种方式，显示后台程序的工作号和PID
  - bg：后台放置一个进程 `bg  %[程序工作号]`
  - fg：在前台放置一个进程 `fg  %[程序工作号]`





