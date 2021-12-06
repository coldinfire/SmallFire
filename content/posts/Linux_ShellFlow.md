---
title: " Shell 脚本-流程控制 "
date: 2019-01-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Linux

tags: 
  - linux
  - shell

---

### 退出状态 
   命令（包括我们编写的脚本和 shell 函数）在终止时向系统发出一个值，称为退出状态。该值是 0 到 255 范围内的整数，表示命令执行成功或失败。按照惯例，值为零表示成功，任何其他值表示失败。
### IF语句 
在shell脚本中，通过上一个命令的退出状态决定是否执行下一个命令，保证多条命令能按顺序正确执行。

`;`：是命令分割符，可以在一行放置多个命令。

```JS
if commands; then
commands
[elif commands; then
commands...]
[else
commands]
fi
```
### IF ELIF ELSE 

```JS
#!/bin/bash

echo -n "Enter a number between 1 and 3 inclusive > "
read character
if [ "$character" = "1" ]; then
    echo "You entered one."
elif [ "$character" = "2" ]; then
    echo "You entered two."
elif [ "$character" = "3" ]; then
    echo "You entered three."
else
    echo "You did not enter a number between 1 and 3."
fi
```

### CASE
语法格式：
```JS
case word in
  patterns ) commands ;;
esac
```
case可以拥有任意数量的模式和陈述。模式可以使文本文字或通配符。可以使用`"|"`分割多个模式。

`"*"`：可以匹配所有的字符，放在最后校验无效的输入
```JS
#!/bin/bash
echo -n "Type a digit or a letter > "
read character
case $character in
  # Check for letters
  [[:lower:]] | [[:upper:]] ) echo "You typed the letter $character"
          ;;
  # Check for digits
  [0-9] )      echo "You typed the digit $character"
          ;;
  # Check for anything else
  * )       echo "You did not type a letter or a digit"
esac
```

### TEST使用 
一般和if联合使用，用来做校验。

有两种表达形式：

   - **test** expression
   - [ expression ]

```JS
if [ -f .bash_profile ]
then
  echo "You have a .bash_profile. Things are fine."
else
  echo "Yikes! You have no .bash_profile!"
fi
```

**常用的判断表达式：**

| Expression            | Description                                  | Expression            | Description                                 |
| --------------------- | -------------------------------------------- | :-------------------- | ------------------------------------------- |
| -d file               | True if *file* is a directory.               | -r file               | True if *file* is a file readable by you.   |
| -e file               | True if *file*exists.                        | -w file               | True if *file* is a file writable by you.   |
| -f file               | True if *file* exists and is a regular file. | -x file               | True if *file* is a file executable by you. |
| -L file               | True if *file* is a symbolic link.           | *file1* -nt *file2*   | True if *file1* is newer than file2.        |
| -z *string*           | True if *string* is empty.                   | *file1* -ot *file2*   | True if *file1* is older than *file2*       |
| -n *string*           | True if *string* is not empty.               | *string1* !=*string2* | True if *string1* does not equal *string2.* |
| *string1* = *string2* | True if *string1* equals *string2.*          |                       |                                             |

### EXIT
在脚本完成时设置退出状态

- exit 0：成功
- exit 1：失败

### 跟踪
可以在脚本中使用set命令打开和关闭跟踪。

- set -x：打开跟踪
- set +x：关闭跟踪

### 循环
在没有接收到退出命令前，一直循环执行。注意循环条件，避免死循环。
#### while
```JS
#!/bin/bash
number=0
while [ "$number" -lt 10 ]; 
do
  echo "Number = $number"
  number=$((number + 1))
done
```
#### until
```JS
#!/bin/bash
number=0
until [ "$number" -ge 10 ]; 
do
  echo "Number = $number"
  number=$((number + 1))
done
```
#### for
```JS
for variable in words; 
do
  commands
done
```
