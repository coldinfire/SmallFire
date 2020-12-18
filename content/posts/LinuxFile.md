---

title: " Linux文件操作 "
date: 2017-10-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux

---

### 文件 ###

  ex: `-rw--w-r-- 1 user group day month time text.xx`

  第一个文件代表意义(0)：

- `d`： 目录

- `-`：文件

- `I`：链接文档

- `b`：可随机存储装置

- `c`：串行端口设备

后续文字意义：

  - 1、三个为一组均为[rwx]组合(r:read w:write x:execute)
     - u=第一组(123)：文件拥有者权限   
     - g=第二组(456)：所属组权限   
     - o=第三组(789)：其他用户权限
  - 2、文件硬链接个数
  - 3、文件拥有者
  - 4、同属者
  - 5、占用内存大小
  - 6、修改时间

#### 修改权限： ####
  ` chmod [xx] file`：修改文件的权限 

   - [u=rwx,g=rwx,o=rwx]/[u+r,w,x...] ：修改各自对应的权限
   - [a=rwx]：修改所有的权限
   - [777]：对应的权限值和，可修改权限   

chown user file:修改文件所有者

 chgrp group file:修改文件所属组

### 处理文件常用命令 ###
•`ls/ll -al ~`：显示所有文件(~显示隐藏) `-i`：查看inode number

•`cd .`：表示当前目录       `cd ..`：表示父目录     `  cd -`：切换到上次所在位置     `cd~`：用户家目录  

•`touch file.xxx`：创建文件或更改文件的时间

•`touch {1,2,3,..}.xxx`：创建多个文件

•`vi/vim file.xxx`：创建并编辑文件

•`pwd`：查看当前绝对路径 

  - 绝对路径：以(/)开头，描述文件完整位置  

  - 相对路径：不以(/)开头，指定相对于当前工作目录而言的位置

•`mkdir [-mp]`：创建新目录 [p]:递归创建 [m]:修改文件权限 777

•`rmdir [-p]`：删除空目录 [p]:删除上层空目录

•`cp [-pdri]`：复制  [p]：连同文件属性复制  [r]：递归持续复制  [d]：复制链接文档而非文件本身  [i]：目标文档已存在，提示是否覆盖

•`rm [-fir]`：移除文件或目录 [f]：忽略不存在文件 [i]：互动模式 [r]：递归删除

创建回收站：myrm(){ D=/tmp/$(date +%Y%m%d%H%M%S); 

- mkdir -p $D; mv "$@" $D && echo "moved to $D ok"; }
   - alias rm= 'myrm'
   - rm [1,2,3].xxx

•`mv [-fiu]`：移动文件或目录，修改名称 [f]：强制 [i]：互动模式 [u]：source较新,update

•远程 rcp: 远程拷贝文件 scp [] 原路径 目标路径：远程拷贝文件，secure copy wget [参数] URL 地址：下载

### 文件内容查看 ###
•`grep []`：查找文件内出现

•`file/stat`：查看文件类型或文件属性信息

•`find [-name]`：在文件系统中的指定目录下查找指定文件

•`cat`：从第一行显示 [n]:行号 [v]:列出特殊字符

•`tac`：从最后一行显示 

•`nl`：显示行号

•`more`：页显示

•`less`：页显示可以往前翻页

•`head`：头几行  [-n number]:显示几行

•`tail`：尾巴几行  [-n number]:显示几行

•`wc -m file_name`：统计文件字符数

•`wc -w file_name`：统计文件文本字数

•`wc -l file_name`：统计文件行数

•`wc file_name`：统计文件行数，字数，字符数

### 链接创建 ###
•`ln f1 f2`：创建f1的一个硬连接文件f2

•`ln -s f1 f2`：创建f1的一个符号连接文件f2

### 压缩解压 ###
#### 文件后缀代表的文件类型： ####

•`.bz2`：用bzip2压缩的文件

•`.gz`：用gzip压缩的文件

•`.tar`：用tar打包的文件

•`.xz`：用xz打包的文件

•`.tbz`：tar打包时用bzip2压缩的文件

•`.tgz`：tar打包时用gzip压缩文件

•`.zip`：用zip/winzip压缩

•`.rar`：用rar压缩文件

•`.7z`：用7za压缩文件

####  压缩方式： ####
• `gzip`：流行的GNU gzip数据压缩/解压程序  【 gzip filename 】

• `bzip2`：性能较高   【 bzip2 filename 】

• `tar`：文件打包，归档工具  【 tar -czvf filename 】
#### 解压方式： ####

• gzip -d filename.gz

• bzip2 -d filename.bz2

• tar -xzvf filename.tar.gz

#### 命令集合 ####
 - file.tar.gz   file.tar.bz2
 - tar -cvf [tar_file.tar] [1,2,3,4]:打包
 - tar -xvf [tar_file.tar]:解包
 - tar -zcvf [tar_file.tar.gz] [1,2,3,4]:压缩打包
 - tar -zxvf [tar_file.tar.gz]:当前目录解压包
 - tar -zxvf [tar_file.tar.gz] -C [指定目录]：解压到指定目录
 - tar -jcvf [tar_file.tar.bz2] [1,2,3,4]:压缩打包
 - tar -jxvf [tar_file.tar.bz2]:解压
 - zip [-r] aim_file  source_file
 - unzip [-d] 解压后目录文件 压缩文件

### I/O重定向 ###
重定向运算符（`“<”` 和 `“>”`）必须出现在命令中的其他选项和参数之后。
#### 标准输出 ####

- 重定向： `>` 是覆盖模式，`>>` 是追加模式， ex：echo "Java3y,zhen de hen xihuan ni" >     qingshu.txt把左边的输出放到右边的文件里去

#### 标准输入 ####
许多命令可以接受来自名为标准输入的工具的输入，从键盘内获取内容，可以被重定向。

从文件重定向标准输入，`“<”`：sort < file.txt   
联合使用：sort < file.txt > sort_file.txt

#### 管道 ####

`cat /etc/group | grep 'sudo'`：将'|'前内容放到管道里，后面对管道进行操作

   - 管道命令 | ：将前面的结果给后面的命令，ex：ls -al | wc，将ls的结果用wc命令来统计字数
   - lpr：接受标准输入并将其发送到打印机
	 - cat poorly_formatted_report.txt | fmt | pr | lpr 
      - cat unsorted_list_with_dupes.txt | sort | uniq | pr | LPR  

`echo`：把内容重定向到指定的文件中 ，有则打开，无则创建   【日志管理使用】

   - echo 算术运算：`${{ expression }}` echo $(($((5**2)) * 3)) 
	- 其中 expression 是由值和算术运算符组成的算术表达式。
	- 算术扩展仅支持整数（整数，无小数），但可以执行许多不同的操作

   - 支持扩展
	   - 从包含大括号的模式创建多个文本字符串 （{A，B，C}，{1..5}，{Z..A}）
	   - a {A {1,2}，B {3,4}} b：aA1b,aA2b,aB3b,aB4b
	   - mkdir {2007..2009}-0{1..9} {2007..2009}-{10..12}

#### 过滤器 ####
   过滤器采用标准输入并对其执行操作，并将结果发送到标准输出。通过这种方式，可以组合起来以强大的方式处理信息。

  - sort：对标准输入进行排序，然后在标准输出上输出排序结果。
  - uniq：它会删除重复的数据行。
  - grep：检查从标准输入接收的每一行数据，并输出包含指定模式字符的每一行。
      - grep 'content' source_rout：筛选出匹配的项
      - grep -v 'content' source_rout：过滤掉匹配的项
  - fmt：从标准输入读取文本，然后在标准输出上输出格式化文本。
  - pr：从标准输入中获取文本输入，并将数据拆分为具有分页符，页眉和页脚的页面，以便为打印做准备。
  - head：输出其输入的前几行。用于获取文件的标题。
  - tail：输出其输入的最后几行。对于从日志文件中获取最新条目等内容非常有用。
  - tr：翻译字符。可用于执行诸如大写 / 小写转换或将行终止字符从一种类型更改为另一种类型的任务。
  - sed：以执行比tr更复杂的文本翻译。
  - awk：一种用于构建过滤器的完整编程语言。非常强大。
