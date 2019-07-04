---

title: " Linux基础 "
date: 2017-10-16T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux

---


## 主要内容 ##
- Linux 操作系统安装及初始化

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

 -  /dev：必要设备

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

man [command]：查看命令使用文档

命令的语法格式：cmd 【options】【arguments】

  - 命令：告诉Linux要做什么

  - 选项：说明运行方式

  - 参数：说明影响操作

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






# 系统工具
```JS
1. 磁盘分析
    df [-ah]:系统磁盘使用信息
    du [-ah]:当前文件的使用
    fdisk [-l] [name]:磁盘分区表操作工具
    mkfs [-t 文件格式] 装置文件名:磁盘格式化 
    mkfs [tab][tab]:查看mkfs支持文件格式
    fsck :磁盘检验
    mount 装置文件名 挂载点：磁盘挂载
    umount [-fn]:卸载 [-f]:强制卸除 [-n]:不升级/etc/mtab下卸除
2. 网络性能  
    ifconfig [card] [x]:网络状态 [-a]:显示全部接口信息 [up/down]:开启关闭网卡 
                                etho:第一块网卡   lo:回环地址
    ps [-aux]  进程
    top        动态进程
    kill [-9] pid:杀死进程 [-9]:强制杀死
```

1. 远程 rcp: 远程拷贝文件 scp [] 原路径 目标路径：远程拷贝文件，secure copy wget [参数] URL 地址：下载

# 用户权限

------

```
1. user:cat /home/...
    who     :查看用户
    whoami  :查看当前用户
    ssh username@ip：远程登录电脑
    useradd name [-mg] :添加用户并自动添加home目录 [-g]:用户组 [-m]:主目录
    passwd name  :修改密码
    su - user   :切换到指定用户(muiot)
    su  user    :to the user
    sudo -s     :切换到root
    su          :to root
    userdel     :del user [-r]:删除用户主目录
    usermod [-aG] group user :change user info  [-a]:添加组 [-g]:group修改默认组 [-aG]:添加其它组
2. user group:cat /etc/group
    查看组:cat /etc/group   groupmod [tab][tab]
    groupmod tabtabtab :check group
    groupadd g_name :add group
    groupdel g_name :del group
    groups username :user in which group
    newgrp root:用户在用户组之间进行切换
3. 重要文件
    /etc/passwd:用户管理涉及到的最重要的一个文件(用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell)
    /etc/group:用户组所有信息(组名:口令:组标识号:组内用户列表)
```

