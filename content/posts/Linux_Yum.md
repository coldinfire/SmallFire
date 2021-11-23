---
title: " Linux 软件包管理 "
date: 2018-04-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - Linux

tags: 
  - linux

---

### 安装包分类
#### 源码包 
源码包：即程序软件的源代码（将软件的源码以 tar 打包后再压缩的资源包）。

RPM 最大的特点就是需要安装的软件已经编译过，并已经打包成 RPM 机制的安装包，通过里头默认的数据库记录这个软件安装时需要的依赖软件。当安装在你的 Linux 主机时，RPM 会先依照软件里头的数据查询 Linux 主机的依赖属性软件是否满足，若满足则予以安装，若不满足则不予安装。

**RPM包命名规则**
以`gcc-4.8.5-36.el7_6.2.x86_64`为例：

  - gcc：软件包名
  - 4.8.5：软件版本
  - 36：软件发布的次数
  - e17_6.2：适合的Linux平台
  - x86_64：适合的硬件平台

 **安装默认路径：** 

 - /etc  配置文件放置目录
 - /usr/bin  一些可执行文件
 - /usr/lib  一些程序使用的动态链接库
 - /usr/share/doc  一些基本的软件使用手册与说明文件
 - /usr/share/man  一些 man page 文件

**查看安装软件路径**

  - `rpm -qa|grep [pack_name]`
  - `rpm -ql [pack_name]`

**RPM的安装依赖性太强，使用yum在线管理来解决安装依赖问题**

#### 二进制包
**二进制包**：如 Red Hat 发行版的.rpm 包，Debian 发行版的.deb 包。

### 源码安装过程

- wget [下载路径]：下载对应的文件
- 解包：tar xvf file.tgz_将下载的压缩文件进行解压
- ./configure：执行configure脚本添加编译参数：
   - ./configure --prefix=/fire_path指定安装路径
- 编译：make
- 安装：make install

### Yum安装

   yum 管理是从指定的服务器（网络 yum 源）下载。YUM 的存在很好的解决了 RPM 的属性依赖问题。YUM 通过依赖 rpm 软件包管理器，实现了 rpm 软件包管理器在功能上的扩展，因此 YUM 是不能脱离 rpm 而独立运行的。yum 提供了查找、安装、删除某一个、一组甚至全部软件包的命令，方便快捷。

#### YUM 的特点

  - 可以同时配置多个资源库 (Repository)

  - 简洁的配置文件 (/etc/yum.conf)

  - 自动解决增加或删除 rpm 包时遇到的依赖性问题

  - 使用方便

  - 保持与 RPM 数据库的一致性

#### YUM 原理说明  

   Server 端先对程序包进行分类后存储到不同 repository 容器中；再通过收集到大量的 rpm 的数据库文件中程序包之间的依赖关系数据，生成对应的依赖关系和所需文件在本地的存放位置的说明文件 (.xml 格式), 存放在本地的 repodata 目录下供 Client 端取用。       

   Cilent 端通过 yum 命令安装软件时发现缺少某些依赖性程序包，Client 会根据本地的配置文件 (/etc/yum.repos.d/*.repo) 找到指定的 Server 端，从 Server 端 repo 目录下获取说明文件 xxx.xml 后存储在本地 /var/cache/yum 中方便以后读取，通过 xxx.xml 文件查找到需要安装的依赖性程序包在 Server 端的存放位置，再进入 Server 端 yum 库中的指定 repository 容器中获取所需程序包，下载完成后在本地实现安装。

#### 网络yum源
yum的一切信息都存储在 `/etc/yum.reops.d`目录下

一般来讲，以 .repo 结尾的文件都是 yum 源。如果能联网，会使用 CentOS-Base.repo 作为默认的 yum 源，如果不能联网我们使用 CentOS-Media.repo 作为本地光盘 yum 源。


```JS
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```
  - [base]：容器名称，放在【】内，本地有多个yum源时，必须保持唯一
  - name：容器说明，可以自定义
  - mirrorlist：镜像站点，可以注释掉
  - basseurl：yum源服务器的地址，可以更改为国内站定，默认是国外站点 
      - 阿里的：http://mirrors.aliyun.com/repo/Centos-7.repo
  - enabled：此容器是否生效，不写默认生效
  - gpgcheck：是否需要验证；0：取消验证；1：使用公钥检验rpm的正确性
  - gpgkey：检验秘钥

`/etc/yum.conf`：该文件是yum的配置文件

```JS
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
gpgcheck=1
plugins=1
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
distroverpkg=centos-release
```
  - cachedir：yum下载的RPM包的缓存目录，存储下载的rpm包和数据库，一般是/var/cache/yum
  - keepcache=0：缓存是否保存，1：保存；0：不包存
  - debuglevel=2：调试级别（0-10），默认2
  - logfile：yum的日志文件所在的位置，默认/var/log/yum.log
  - distroverpkg：指定一个软件包，yum 会根据这个包判断你的发行版本，默认是 redhat-release
  - exactarch：有两个选项 1 和 0, 代表是否只升级和你安装软件包 cpu 体系一致的包

#### yum命令

yum makecache

**清空缓存列表**

 - yum clean packages：清除缓存目录下的软件包，清空的是 (/var/cache/yum) 下的缓存
 - yum clean headers：清除缓存目录下的 headers
 - yum clean oldheaders：清除缓存目录下旧的 headers
 - yum clean, yum clean all： (= yum clean packages; yum clean oldheaders) 清除缓存目录下的软件包及旧的 headers

**查询安装包**

 - yum info [package_name]：显示指定安装包详细信息
 - yum info updates：列出所有可更新的软件包信息
 - yum info installed：列出所有已安裝的软件包信息
 - yum list：查询所有可用软件包列表
 - yum list [package_name]：显示指定程序包安装情况
 - yum list updates：列出所有已安装的软件包
 - yum search [keyword]：查询服务器上和关键字相关的软件包
 - yum provides [package_name]：列出软件包提供哪些文件

**安装**

 - yum -y install [package_name..]：-y自动回答yes；-q不显示安装过程
 - yum list [package_name]：查询安装是否成功
 - yum reinstall [package_name..]：重新安装

**升级**

 - yum check-update：检查可更新的程序
 - yum upgrade [package_name]：升级指定程序包
 - yum update [package_name]：更新指定软件包
	- 如果不指定包名，那么将会升级系统中所有的软件包，包括 Linux 内核。

**卸载**

 - yum remove [package_name]：卸载软件包
	- 卸载和升级也一样，而且由于软件包很多都有依赖性，你卸载 A，而 B 和 C 都依赖于 A，那么 B 和 C 都会卸载。

 - yum deplist rpm：查看程序rpm依赖情况


#### yum软件组管理
安装某个软件组，会比我们一个一个安装某个软件包要方便的多。

  - yum grouplist：列出所有可用的软件组列表
  - yum groupinstall [group_name]：安装指定软件组
  - yum groupremove [group_name]：卸载指定软件组



