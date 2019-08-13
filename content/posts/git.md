---

title: "Git学习与总结"
date: 2018-03-11
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Git

---

## Git本地结构

![git](/images/git/git.jpg)

​	**Workspace：**工作区，实际操作的目录。

​	**Index / Stage：**暂存区，数据暂时存放的区域，可在工作区和版本库之间进行数据的友好交流。

​	**Repository：**仓库区（或本地仓库）工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。版本库里面存了很多东西，其中最重要的就是暂存区，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

​	**Remote：**远程仓库，github上的仓库或其他服务上的仓库。

## Git远程代码库

------

​	git与github远程代码库，进行操作。

**团队协作：**

![git-github](/images/git/github.jpg)

**跨团队协作：**

![git-github](/images/git/github2.png)

## Git设置签名

------

​	目录位置：.git/config文件

​	**仓库级别：**仅在当前本地库范围生效，优先级(就近原则)

- git config user.name [user_name]
- git config user.email [user_email]

​	**系统级别：**登录当前操作系统的用户范围

- git config ***- -global*** user.name [user_name]
- git config ***- -global*** user.email [user_email]

- git config - -list：显示当前的Git配置

-  git config -e [- -global]：编辑Git配置文件

## Git命令

------

### 常用的Git命令

```JS
add	       添加文件内容至索引
bisect	   通过二分查找定位引入 bug 的变更
branch     列出、创建或删除分支
checkout   检出一个分支或路径到工作区
clone      克隆一个版本库到一个新目录
commit     记录变更到版本库
diff       显示提交之间、提交和工作区之间等的差异
fetch      从另外一个版本库下载对象和引用
grep       输出和模式匹配的行
init       创建一个空的 Git 版本库或重新初始化一个已存在的版本库
log        显示提交日志
merge      合并两个或更多开发历史
mv         移动或重命名一个文件、目录或符号链接
pull       获取并合并另外的版本库或一个本地分支
push       更新远程引用和相关的对象
rebase     本地提交转移至更新后的上游分支中
reset      重置当前HEAD到指定状态
rm         从工作区和索引中删除文件
show       显示各种类型的对象
status     显示工作区状态
tag        创建、列出、删除或校验一个GPG签名的 tag 对象
```

### 命令解析

- 初始化：
  - git init：初始化本地Git仓库,产生.git目录，该目录不要进行操作

- 增加和删除文件：
  -  git add [file1] [file2]...：添加指定文件到暂存区 
  - git add [dir]：添加指定目录到暂存区,包括子目录
  - git add . / git add -A：添加当前目录的所有文件到暂存区
  - git add -p：添加每个变化前，都会要求确认,对于同一个文件的多处变化，可以实现分次提交 
  -  git rm [file1] [file2]...：删除工作区文件，并且将这次删除放入暂存区
  - git rm - - cached [filename]：将文件从暂存区删除
  - git mv [file-original] [file-renamed]：改名文件，并且将这个改名放入暂存区
- 代码提交
  -  git add . + git commit -m "xxx"：提交修改
  - git commit -m "message" [filename]：提交暂存区到仓库
  - git commit [file1] [file2] ... -m "message"：提交暂存区的指定文件到仓库区
  - git commit -a：提交工作区自上次commit之后的变化，直接到仓库区
  - git commit -v：提交时显示所有diff信息
  - git commit - -amend [file1] [file2]：重做上一次commit，并包括指定文件的新变化
- 查看信息
  - git status：查看工作区是否还有文件未提交
  - git diff [file_name]：显示暂存区和工作区的差异
  - git diff head [file_name]：显示工作区和版本库差异
  - git diff - -cached：显示暂存区和版本库差异
  - git diff file.xxx：查看文件的修改内容
  - git log：查看版本历史(git log命令显示从最近到最远的显示日志)
  - git log - -pretty=oneline：查看简短历史
  - git log - -oneline：每条记录显示在一行，更加简洁，缩短hash值，只显示过去的记录
  - git reflog：查看所有产生的版本号
  - git log - -stat：显示commit历史，以及每次commit发生变更的文件
  - git log -S [keyword]：根据关键词搜索提交历史
  -  git diff [first-branch]...[second-branch]：显示两次提交之间的差异

![git_log](/images/git/gitlog.jpg)

## Head

------

- Head的本质不是指向分支，而是指向commit提交。

- HEAD 通过先指向分支的头指针，再指向提交的意义就是表明当前所处的分支。

- 当 HEAD 指针直接指向提交时，就会导致 detached HEAD 状态。在这个状态下，如果创建了新提交，新提交不属于任何分支。
  相对应的，现存的所有分支也不会受 detached HEAD 状态提交的影响。

​	进入detached HEAD：
​	`git checkout <commit id>   git checkout --detach`

- HEAD : 指向当前正在操作的commit
- ORIG_HEAD : 记录危险操作之前的HEAD,方便HEAD的恢复,修改前的备份
- FETCH_HEAD : 记录从远程仓库拉取的记录
- MERGE_HEAD : git merge时,记录正在合并到你的分支中的提交
- CHERRY_PICK_HEAD : 记录在运行git cherry-pick时要合并的提交

## Cherry-pick

------

- git cherry-pick : 选择将现有的一个或者多个提交的修改引入当前内容

- git merge : 将两个提交历史合并,操作对象是commit history

- git cherry-pick : 将提交对应的内容合并,操作对象是commi、commit代表的是修改

  

## 版本回退

------

git reset - -hard [hash_version]：回退到对应的版本号,推荐使用

git reset - -hard HEAD^：回退到上个版本,使用`^`

git reset - -hard HEAD^^：回退到上上个版本

git reset - -hard HEAD~n：回退到第n个版本，使用`~`

- rest三个参数：
  - --soft 参数：仅在本地库移动HEAD指针
  - --mixed 参数：在本地移动HEAD指针，重置暂存区
  - --hard参数：在本地移动HEAD指针，重置暂存区，重置工作区



撤销修改：git checkout [file]，撤销修改回到添加暂存区后的状态

恢复某个commit : git checkout [commit] [file]

恢复暂存区的所有文件到工作区 : git checkout .

删除文件：rm file.xxx  彻底删除需要执行commit提交。未commit之前，使用 git checkout -- file.xxx在版本库中恢复文件

删除尚未提交版本库：`git reset --hard HEAD`

永久删除文件找回：可使用版本回退方式找回文件。删除前，文件存在时的状态已经提交到本地库。



## 分支

------

- 主要分支
  - master : origin/master 分支上的最新代码永远是版本发布状态
  - dev : 最新的开发进度,当dev上的代码达到一个稳定的状态，可以发布版本的时候,会以某种特别方式被合并到master分支上,然后标记上对应的版本标签
  - Feature : 用来做分模块功能开发,模块完成之后,会合并到dev分支,然后删除自己
  - Release : 用来做版本发布的预发布分支,测试中出现的小问题,在release分支进行修改提交,测试完毕准备发布的时候,代码会合并到master和dev,然后删除自己
  - Hotfix : 用来做线上的紧急bug修复的分支,当线上某个版本出现了问题,将检出对应版本的代码,创建Hotfix分支,问题修复后,合并回master和dev,然后删除自己
- 分支操作命令
  - git branch [-v]：查看所有本地分支，-v 详细信息
  - git branch -r：列出所有远程分支 :
  - git branch -a：列出所有分支 
  - git branch b_name：新建一个分支，但依然停留在当前分支
  - git branch --track [branch] [re-branch]：新建一个分支，与指定的远程分支建立追踪关系
  - git checkout b_name：切换分支
  - git checkout  - ：切换到上一个分支
  - git checkout –b name：创建+切换分支
  - git merge  from_b_name：合并指定分支到当前所在分支
  - git branch –d [b_name]：删除分支
  - git push origin --delete [b_name] / git branch -dr [remote/branch] ：删除远程分支

冲突解决

------

​	Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，其中<<<<<< HEAD是指主分支修改的内容，>>>>>> b_name是指别的分支上修改的内容`resove the problem the you could merge your code.`

​	当解决完冲突后，使用git add file > > git commit -m "Msg" :完成merge.

**分支管理策略：** 通常合并分支时，git一般使用”Fast forward”模式，在这种模式下，删除分支后，会丢掉分支信息，现在我们来使用带参数 --no-ff 来禁用”Fast forward”模式。

**分支策略：**首先master主分支应该是非常稳定的，也就是用来发布新版本，一般情况下不允许在上面干活，干活一般情况下在新建的dev分支上干活，
干完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来

**Bug分支：**在Git中，分支是很强大的，每个bug都可以通过一个临时分支来修复，修复完成后，合并分支，然后将临时的分支删除掉。

Git还提供了一个stash功能，可以把当前工作现场 ”隐藏起来”，等以后恢复现场后继续工作.比如我现在是在主分支master上来修复的，现在我要在master分支上创建一个临时分支issue-404.修复完成后，切换到master分支上，并完成合并，最后删除issue-404分支。工作区是干净的，那么我们工作现场去哪里呢？我们可以使用命令 git stash list来查看下.
需要恢复一下，可以使用如下2个方法：

​	1. git stash apply恢复，恢复后，stash内容并不删除，你需要使用命令git stash drop来删除。

​	2. 另一种方式是使用git stash pop,恢复的同时把stash内容也删除了。

## 标签

------

- git tag：列出所有tag 

- git tag [tag]：新建一个tag在当前commit

- git tag [tag] [commit]：新建一个tag在指定commit

- git tag -d [tag]：删除本地tag

- git push origin :refs/tags/[tagname]：删除远程tag

- git show [tag]：查看tag信息
- git push [remote] [tag]：提交指定tag
- git push [remote] --tags：提交所有tag

- git checkout -b [branch] [tag]：新建一个分支，指向某个tag



## 远程同步

------

- git remote add [origin] [git_url]：加一个新的远程仓库，并命名origin
- git remote –v：查看远程库的详细信息 
- git remote show [origin]：查看某个远程库的信息
- git fetch [origin]：下载远程仓库的所有变动

- git fetch [origin] [master(Remote)]：下载远程仓库指定分支的所有变动
- git merge [origin/master]：将远程仓库对应的master 分支合并到本地分支
- git pull [origin] [master(Remote)]：取回远程仓库的变化，并与本地分支合并
- git push [origin] [master(Local)]：上传本地指定分支到远程仓库
-  git push [origin] - -all：推送所有分支到远程仓库 
- git push [origin] - -force：强行推送当前分支到远程仓库，即使有冲突 
- git clone [git_url] [loc_url]：克隆远程库到指定文件夹

**免密登录：**只允许配置一个用户

- ​	`ssh-keygen -t rsa -C [email]：`会在`~`目录下的`.ssh`文件生产两个文件，id_rsa、id_rsa.pub(公钥).

- 将公钥添加到Github的`SSH keys`

## GitFlow

------

![Git工作流](/images/git/branch.jpg)



## 多人协作工作模式

------

**一般场景：**首先，可以试图用`git push origin [branch-name]`推送自己的修改.

- ​    如果推送失败，则因为远程分支比你的本地更新早，需要先用git pull试图合并。
- ​    如果合并有冲突，则需要解决冲突，并在本地提交。再用git push origin branch-name推送.

**推送分支：**

​	推送分支就是把该分支上所有本地提交到远程库中，推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

​	使用命令 `git push origin master`，master分支是主分支，因此要时刻与远程同步。一些修复bug分支不需要推送到远程去，可以先合并到主分支上，然后把主分支master推送到远程去。

**抓取分支：**多人协作时，大家都会往master分支上推送各自的修改。

​	使用命令创建本地dev分支：`git checkout –b dev origin/dev`，现在可以在dev分支上做开发了，开发完成后把dev分支推送到远程库时。
   	  如果别人已经向origin/dev分支上推送了提交，而我在我的目录文件下也对同样的文件同个地方作了修改，也试图推送到远程库时,会推送失败，因为他人最新提交的和我试图推送的有冲突。解决的办法也很简单，上面已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后在本地合并，解决冲突，再推送。

- 指定本地dev分支与远程origin/dev分支的链接：`git branch --set-upstream-to=origin/dev.`git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的 解决冲突完全一样。解决后，提交，再push到远程库里面去。



**跨团队合作：**

- 远程团队Fork仓库内容到其仓库
- 远程团队Clone仓库到其本地
- 远程团队对其本地仓库进行修改操作
- 远程团队将修改好的内容提交到其远程仓库
- pull request到管理团队审核人，审核人对pull request进行审核
- 审核通过，merge pull request，将代码合并到该团队的远程仓库
- 本地团队可将远程仓库代码拉取到本地进行操作



## Git Submodule

------

### Submodule

`git Submodule` 是一个很好的多项目使用共同类库的工具，他允许类库项目做为 `repository`, 子项目做为一个单独的 `git项目`存在父项目中，子项目可以有自己的独立的 `commit`，`push`，`pull`。而父项目以 `Submodule` 的形式包含子项目，父项目可以指定子项目 `header`，父项目中会的提交信息包含 `Submodule` 的信息，在 `clone父项目`的时候可以把 `Submodule` 初始化。

- 在当前文件夹添加Submodule
  - git submodule add [子项目git地址]
  - 会在文件夹下生成 .gitmodules文件，包含Submodule主要消息，指定repository
- 修改Submodule,需要有Commit权限
  - 打开需要操作的文件夹，git status查看状态
  - git commit -a -m 'modify submodule'：提交更改的内容
  - git push：push到子项目远程服务器
- 在父目录中查看状态会带有(new commits)标识,将变动信息同步到父项目的远程服务器
  - git commit -m 'modify submodule'
  - git push：push到父项目远程服务器
- 更新submodule
  - (1) 打开相应的submodule目录：git pull
  - (2) 在父目录下直接运行：git submodule foreach git pull
- clone submodule
  - (1) 递归clone整个项目：git clone [项目git地址] --recursive
  
  - (2) 先clone父项目，再初始化Submodule： git clone [项目git地址] 》 cd [submodule_目录] 》 git submodule init > git submodule update
    
  
- 删除submodule
  
  -  git rm [submodule name]

## 不同开发平台管理

------

**保持专注性**

- 对语言进行包的忽略

- 对平台配置文件进行忽略

- 对编译成的文件进行忽略

  