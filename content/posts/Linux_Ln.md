---
title: " Linux 链接和网络管理 "
date: 2018-04-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - Linux

tags: 
  - linux

---


### 软链接 ###

 创建：`ln -s 源路径 目标路径`

 特点：删除链接文件，源文件无影响；***删除源文件，链接文件失效***；修改其中一个文件，两个内容都改变；可以跨分区

 指向：新创建的软链接指向源文件，源文件删除会失去指向


### 硬链接 ###

 创建：`ln 源路径 目标路径`

 特点：删除链接文件，源文件无影响；***删除源文件，链接文件无影响***；修改其中一个文件，两个内容都改变；不能跨分区

 指向：新创建的软链接指向源文件的inode number，源文件删除，使用的inode number块仍然存在

### 网络管理 ###

查看网卡：ifconfig [net_name]

配置静态IP:

 - 找到并修改文件`/etc/sysconfig/network-scripts/ifcfg-xxx` 
	 - ONBOOT=YES：reboot & service network restart 时被激活
	 - BOOTPROTO=STATIC

打卡网卡：config 网卡名 up

关闭网卡：config 网卡名 down

重启网络服务：service network restart 

重启服务器：reboot
