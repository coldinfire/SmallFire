---
title: " Shell 脚本 "
date: 2018-04-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux
  - shell

---

## 什么是Shell脚本 ##
简单来说，shell 脚本是包含一系列命令的文件。shell 读取此文件并执行命令，就像它们已直接在命令行上输入一样。shell 有点独特，因为它既是系统的强大命令行界面，也是脚本语言解释器。

将Shell脚本放置在当前用户的`bin`目录下，使用Shell脚本文件名称可直接执行该Shell脚本。

### 环境建立 ###
登录系统时，bash 程序启动，并读取一系列称为启动文件的配置脚本。这些定义了所有用户共享的默认环境。接下来是主目录中的更多启动文件，用于定义您的个人环境。

#### 登录Shell ####

   - `/etc/profile`：适用于所有用户的全局配置脚本。
   - `~/.bash_profile`：用户的个人启动文件。可用于扩展或覆盖全局配置脚本中的设置。
   - `~/.bash_login`：如果找不到〜/ .bash_profile，bash 会尝试读取此脚本。
   - `~/.profile`：如果找不到〜/ .bash_profile，~/.bash_login，bash 会尝试读取此脚本。

#### 非登录Shell ####

  - /etc/bash.bashrc：适用于所有用户的全局配置脚本。
  - ~/.bashrc： 用户的个人启动文件。可用于扩展或覆盖全局配置脚本中的设置。

### Shell编写 ###
变量：

  - 定义：var_name="自定义内容"，’=‘两边不允许有空格
  - 使用：$var_name即可使用该变量
  - 变量可以嵌套使用：time_stamp="由 $USER 更新 $（date +“％x％r％Z")"

环境变量：

 - printenv：查看当前系统的环境变量

常量：一旦设置就永远不应该改变的值，大写字符

### Shell函数 ###
在Shell中定义函数，通过函数将各个功能封装起来，在整个shell脚本可以多处调用。

```JS
## Constants常量：
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"
## Funcitons定义：
drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"
}
## Main调用：
<body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(drive_space)
</body>

```
### Read
获取键盘输入值。

- `read -t text.`：指定秒数后放弃
- `read -n text.`：不显示用户输入值

```JS
echo -n "Enter some text > "    //-n：光标保持在一行
read text
echo "You entered: $text"
```
### 位置参数
位置参数是一系列包含命令行内容的特殊变量（$0到$9）

$＃的变量，该变量 除了命令名（$0）外还包含命令行中的项数