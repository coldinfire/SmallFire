---

title: " Linux 权限管理 "
date: 2017-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux

---
## 用户权限

### 增删改查用户
#### 增 
   useradd username：创建用户         
   passwd username：设置用户密码 

   - -u：指定用户的UID          
   - -g：修改用户GID       
   - -d：修改用户的家目录
   - -c：修改用户的备注      
   - -s：修改用户所用的shell

  生成文件：

 - /etc/passwd :  用户名：密码：UID：GID：Desc：用户目录：是否是可执行文件
 - /etc/shadow :  密码信息
 - /etc/group : 组文件【组名：组密码标识：GID：组成员】        
 - /etc/gshadow : 组密码
 - /home/username：家目录
 - /var/spool/mail : 邮件信息

#### 查 
 id username : 查看用户的UID，GID，所属组信息。
#### 改 ####
 usrmod：修改用户信息  

   - -u：修改用户的UID    
   - -g：修改用户GID      
   - -G(覆盖)：将用户加到指定组  a:追加
   - -d：修改用户的家目录  
   - -c：修改用户的备注信息  
   - -s：修改所用shell

#### 删 ####
 userdel -r username:删除用户

#### Other： ####

切换用户：  su：切换到root用户      su - username：切换用户

锁定用户：usrmod -L username         

解锁：usermod -U username

新建组：groupadd group_name

- 用户修改所属组：usermod  -aG  group_new  aim_group

### 文件权限  ll  ###

 文件详细信息：  drwxrwxr-x. 2 fogweek fogweek 4096  7月  10 20:08  test1

 - 1：文件类型    (-:普通文件),(d:目录),(l:软连接),(b:设备),(p:管道文件)
 - 2-4：属主(U)权限(r:read，w:write，x:excuse)
 - 5-7：属组(G)权限(r:read，w:write，x:excuse)
 - 8-10：其他(O)用户权限(r:read，w:write，x:excuse)
 - 硬链接数    
 - 属主
 - 属组   
 - 文件大小   
 - 文件创建日期  
 - 文件名称

#### 修改权限 ####
 - 修改文件权限：chmod    
	 - chmod u=rws filename
 -  修改所属组：
	 -  chown  UID.GID  filename       
	 -   chown UID filename     
	 -   chown  .GID  filename
 -  执行文件：./filename      
	 -  . filename       
	 -  sh filename   
	 -  bash filename
 -  数字表示：R:4，W:2，X:1

### 目录权限  查看（ll -d director_path） ###

 - r：可以ls该目录下的子文件名，子目录名

 - w：可以在该目录下创建，删除，重命名

 - x：可以cd到该目录下
#### 修改文件夹权限 ####
  -  修改文件夹权限：chomd    [chomd  777  direct_name]
  -  修改所属组：
	  -  chown  UID.GID  filename   
	  -  chown UID filename  
	  -  chown  .GID  filename
	  -  chown -R  UID.GID  dir_name：d


### 用户信息 ###
 user：cat /home/...

   - who：查看用户
   - whoami：查看当前用户
   - ssh username@ip：远程登录电脑
   - useradd name [-mg]：添加用户并自动添加home目录 [-g]：用户组 [-m]：主目录
   - passwd name：修改密码
   - su - user ：切换到指定用户(muiot)
   - su  user：to the user
   - sudo -s：切换到root
   - su：to root
   - userdel：del user [-r]：删除用户主目录
   - usermod [-aG] group user ：change user info  
	   - [-a]：添加组 
	   - [-g]：group修改默认组 
	   - [-aG]：添加其它组

### 用户组信息 ###
user group：cat /etc/group

  -  查看组：cat /etc/group   groupmod [tab][tab]
  -  groupmod tabtabtab：check group
  -  groupadd g_name：add group
  -  groupdel g_name：del group
  -  groups username：user in which group
  -  newgrp root：用户在用户组之间进行切换

### 重要文件 ###
   - /etc/passwd:用户管理涉及到的最重要的一个文件(用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell)
   - /etc/group:用户组所有信息(组名:口令:组标识号:组内用户列表)
