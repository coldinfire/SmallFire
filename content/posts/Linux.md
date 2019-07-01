---

title: " Linux基础 "
date: 2019-01-16T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux

---



# 基本命令

```
man [command]:查看命令使用文档
1. 文件
  ex: -rw--w-r-- 1 user group day month time text.xx
  第一个文件代表意义(0)：
    [d]:目录
    [-]:文件
    [l]:链接文档
    [b]:可随机存储装置
    [c]:串行端口设备
  后续文字意义：
    <1>.三个为一组均为[rwx]组合(r:read w:write x:execute)
    u=第一组(123)：文件拥有者权限   g=第二组(456)：所属组权限   o=第三组(789)：其他用户权限
    <2>.文件硬链接个数
    <3>.文件拥有者
    <4>.同属者
    <5>.大小
    <6>.修改时间
  修改权限：
    chmod [xx] file:修改文件的权限 
                    [u=rwx,g=rwx,o=rwx]/[u+r,w,x...] :修改各自对应的权限 [a=rwx]:修改所有的权限
                    [777]:对应的权限值和，可修改权限
    chown user file:修改文件所有者
    chgrp group file:修改文件所属组
2. 处理文件常用命令：
    ls/ll -al ~:显示所有文件(~显示隐藏)
    cd:切换目录
    touch file.xxx:创建文件
    touch {1,2,3,..}.xxx :创建多个文件
    vi/vim file.xxx:创建并编辑文件
    pwd [-p]:显示当前所在目录 [p]:真是路径而非link路径
    mkdir [-mp]:创建新目录 [p]:递归创建 [m]:修改文件权限 777
    rmdir [-p]:删除空目录 [p]:删除上层空目录
    cp [-pdri]:复制  [p]:连同文件属性复制  [r]递归持续复制  [d]复制链接文档而非文件本身  [i]目标文档已存在，提示是否覆盖
    rm [-fir]:移除文件或目录 [f]忽略不存在文件 [i]互动模式 [r]递归删除
    创建回收站：myrm(){ D=/tmp/$(date +%Y%m%d%H%M%S); mkdir -p $D; mv "$@" $D && echo "moved to $D ok"; }
            alias rm= 'myrm'
            rm [1,2,3].xxx
    mv [-fiu]:移动文件或目录，修改名称 [f]强制 [i]互动模式 [u]source较新,update
3. 文件内容查看
    grep []:查找文件内出现
    find []:查找文件
    cat:从第一行显示 [n]:行号 [v]:列出特殊字符
    tac:从最后一行显示 
    nl:显示行号
    more:页显示
    less:页显示可以往前翻页
    head:头几行  [-n number]:显示几行
    tail:尾巴几行  [-n number]:显示几行
4. 链接创建
    ln f1 f2:创建f1的一个硬连接文件f2
    ln -s f1 f2:创建f1的一个符号连接文件f2
5. 压缩解压
    file.tar.gz   file.tar.bz2
    tar -cvf [tar_file.tar] [1,2,3,4]:打包
    tar -xvf [tar_file.tar]:解包
    tar -zcvf [tar_file.tar.gz] [1,2,3,4]:压缩打包
    tar -zxvf [tar_file.tar.gz]:当前目录解压包
    tar -zxvf [tar_file.tar.gz] -C [指定目录]：解压到指定目录
    tar -jcvf [tar_file.tar.bz2] [1,2,3,4]:压缩打包
    tar -jxvf [tar_file.tar.bz2]:解压
    zip [-r] 目标文件 源文件
    unzip [-d] 解压后目录文件 压缩文件
6. 管道
    cat /etc/group | grep 'sudo' :将'|'前内容放到管道里，后面对管道进行操作
```

# 系统工具

------



```
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

# vim

------



```
1.使用
    vim/vi filename =====>命令模式=====>i,a,o=====>输入模式======>ESC=====>命令模式
    命令模式=====>:=====>底线命令模式=====>:wq!(退出)
    :set nu (显示行号)     :set nonu (取消行号)
```

# 下载 yum

------

