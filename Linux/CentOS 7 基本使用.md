---
title: CentOS 7 基本使用(TBD)
tags: [开发,Linux,CentOS,Java]
toc: true
categories: 开发 - Linux
description: "简要介绍了CentOS 7 的基本使用"
---

# CentOS 7 基本使用

> [Linux 教程](http://www.runoob.com/linux/linux-tutorial.html)
>
> [30 Things to Do After Minimal RHEL/CentOS 7 Installation](http://www.tecmint.com/things-to-do-after-minimal-rhel-centos-7-installation/2/)

[TOC]

## Shell 命令解释

https://explainshell.com/

## 添加用户到sudoers

命令： 
```shell
su -
***   # root用户的密码
vim /etc/sudoers
# 若无写入权限，则 chmod u+w /etc/sudoers
# 找到 Allow root to ruan any commands anywhere
# 找到 root  ALL=(ALL)  ALL，在下面新加一行
:i # vim编辑
<username>  ALL=(ALL)  ALL
:wq # vim 保存退出
```

## 系统信息查看
- 查看linux版本  `lsb_release -a`
- 查看端口占用情况  `lsof -i tcp:8080`
- 列出所有端口  `netstat -ntlp`
- 查看所占用的目录大小 `du -sh` (不显示子目录大小，以人性化方式显示（Mb）)
- 查看所有文件系统 `df -h` （磁盘目录）
## 环境变量

- shell 中临时设置，立即生效

  `export PATH=/usr/local/mongodb/bin:$PATH `

- `~/.bashrc  `  新开终端生效，或者`source ~/.bashrc ` 命令立即生效 

  仅对当前用户有效

  `vim ~/.bashrc` ， 添加   `export PATH=/usr/local/mongodb/bin:$PATH `

- `/etc/profile `文件，系统重启后生效

  对所有用户有效

  `vim /etc/profile`  ， 添加 ` export PATH=/usr/local/mongodb/bin:$PATH `

>[CentOS查看和修改PATH环境变量的方法](https://blog.csdn.net/boolbo/article/details/52437760) 
>
>[Linux中环境变量文件profile、bashrc、bash_profile之间的区别和联系](https://blog.csdn.net/zhanglh046/article/details/52373440) 



## 防火墙

> [不可不知的centos7 firewalld 防火墙的使用](https://segmentfault.com/a/1190000003931716) / [CentOS 7 firewalld使用简介](http://www.centoscn.com/CentOS/help/2015/0208/4667.html)
>
> 开启 firewalld之后，使用iptables设置的防火墙不管用了，需要使用firewalld配置

使用`systemctl status firewalld`查看状态，若为`Active: inactive (dead) ` 或执行命令时显示` FirewallD is not running`，可以执行 `systemctl start firewalld`  开启防火墙。

可视化管理

`firewall-config &`

开启端口

`firewall-cmd --zone=public --add-port=80/tcp --permanent`

>有 runtime 和 permanent 设定两种，runtime  设定 reload 后会恢复原状态，而 permanent  不会。 

重启防火墙

`firewall-cmd --reload`

添加 EPEL源（ Extra Packages for Enterprise Linux）

`sudo yum install epel-release` 

**Zone**

![这里写图片描述](../../github-blogs/source/_posts/CentOS 7 基本使用/SouthEast)

## 查看帮助
http://www.centoscn.com/CentOS/help/2014/0220/2419.html

- `man 命令`
- `whatis 命令`
- `命令 -？`
- `命令 --help 或 help  -命令` 前者属于外部命令帮助，后者属于内部命令帮助。使用`type 命令`查看命令类型，有 builtin为内部命令

`-?, --help`                 显示此帮助列表
```shell
--restrict           禁用某些潜在的有危险的选项
--usage              显示简短的用法说明
--version             打印程序版本
```

##设置终端语言

当系统安装时，为使用方便，我们需要设置系统语言为中文。但是有时候却需要终端输出为英文，例如出现错误信息需要Google时。

此时我们只需要设置终端的语言即可，不需要去设置系统的语言，即不需要去修改系统配置文件。

```shell
locale -a # 查看所有可设置的语言
LANG=en_US.UTF-8 bash  # 切换为英文  或  en_GB.UTF-8
LANG=zh_CN.UTF-8 bash  # 切换为中文
```

## 程序安装/查看

### 软件源

```shell
yum repolist # 列出本机所有源
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak # 备份默认yum源  
```

**清华大学镜像**：

https://mirror.tuna.tsinghua.edu.cn/help/centos/

https://mirrors.tuna.tsinghua.edu.cn/help/epel/

```shell
yum install epel-release
vim /etc/yum.repos.d/epel.repo  # centos 源则为 CentOS-Base.repo
# 取消注释 baseurl开头的行，并注释mirrorlist 开头的行
# 把文件里的 http://download.fedoraproject.org/pub 替换成 https://mirrors.tuna.tsinghua.edu.cn
yum makecache # 生成缓存
```

**fedoraproject**：

`su -c 'rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm'` 

**aliyun**:

```shell
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
# 下载新的 CentOS-Base.repo 到/etc/yum.repos.d/
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

[**调整软件源顺序**](https://www.jianshu.com/p/9c74838b675f)

```shell
yum -y install yum-priorities # 使用yum-priorities插件
```

设置`/etc/yum.repos.d/ `目录下的`.repo`相关文件（如CentOS-Base.repo），在这些文件中插入顺序指令：`priority=N `（N为1到99的正整数，数值越小越优先）
一般配置`[base], [addons], [updates], [extras]` 的`priority=1`，`[CentOSplus], [contrib] `的`priority=2`，其他第三的软件源为：`priority=N `（推荐N>10）

如

```
[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
priority=1
```

最后`yum makecache # 生成缓存`

### rpm（**RedHat Package Manager**）软件操作

####软件库

- http://rpm.pbone.net/
- https://pkgs.org/

#### 安装

`rpm -i <软件包>`  安装rpm软件（静默安装）；

`-v `  显示详细的安装信息；`-h`显示安装进度。

`rpm -vih <软件包>`  显示详细的安装信息，并显示进度

#### 卸载
`rpm -e <软件包>`
#### 更新
`rpm -U <软件包>` 删除原软件，仅保留配置文件，然后安装新版本
### rpm 命令查看程序信息
一般有源码和包安装两种方式.
- 源码安装的话可以看 configure 的日志;
- rpm 等包方式的话,就要查其中的数据库了,比如 `rpm -q `进行查询.
- `-q`  <== 查询(查询本机已经安装的包时不需要版本名称)
- `-qi `  #查询被安装的包的详细信息(information)
- `-qa | grep dhcp`  <== 列出所有被安装的rpm package 
- `-qc` 列出配置文件(/etc下的文件)
- `-qd` 列出帮助文件(man)
- `-ql dhcp `   <== 查询指定 rpm 包中的文件列表
- `-qf /bin/ls`  <== 查询哪个库里包含了 ls 文件(注意，需要安装了 `/bin/ls` 后才能查到)
- `-qp < rpm package name> ` <== 根据rpm包查询(`.rpm` 文件),可以接其他参数(如`i`查详细信息，`l`查文件列表 等)
- `-qR` 列出需要的依赖套件 
### 软件安装信息-yum

```shell
yum list installed <软件包名>* # 是否通过 yum 安装过任意版本的软件
yum list | grep packageName
# 列出所有可安装的软件包
yum list
# 列出所有可更新的软件包
yum list updates
# 列出所有已经安装但不在repository的包
yum list extras
# 查看软件包的详细信息
yum info ..
# 清除yum缓存
yum clean ..
yum clean all
# yum clean headers清除header，yum clean packages清除下载的rpm包，yum clean all一全部清除

yum search 'keyword'
autoconf -V # 查看版本
yum info autoconf # 查看安装信息
which autoconf # 
yum install 'package_name'
yum -y install 'package_name'  # 自动选择 yes
yum install 'package_name'  --installroot='install_path'
yum remove 'package_name'

rpm -q <软件包名>  # 是否安装（没安装就没有安装信息）
rpm -qa <软件包名>   # 列出包的安装信息
rpm -qa|grep <软件包名> # 列出包含<软件包名>字段的软件的信息。
rpm -ql <软件包名> # 查看软件安装位置 如 rpm -ql postgresql95-server
```



RPM默认安装路径：

| /etc           | 一些设置文件放置的目录如/etc/crontab |
| -------------- | ------------------------------------ |
| /usr/bin       | 一些可执行文件                       |
| /usr/lib       | 一些程序使用的动态函数库             |
| /usr/share/doc | 一些基本的软件使用手册与帮助文档     |
| /usr/share/man | 一些man page文件                     |

### 查看程序安装目录

`rpm -ql 程序名称`

### CentOS 7 中 yum 安装 ntfs-3g
CentOS默认源里没有ntfs3g，想要添加ntfs支持，无非是自己下载编译安装或者加源yum安装。

```shell
yum update; yum install ntfs-3g # 更新？
yum install ntfs-3g
```

##进程

> 程序后台运行（即使关闭终端）：`nohup 命令&`

```shell
# 进程总数
ps -ef| wc -l
ps -ef| grep httpd | wc -l
ps -aux | grep '进程名称'
# ps -au(x) 输出格式 :
# USER PID %CPU（占用率） %MEM VSZ（占用虚拟内存） RSS（占用内存） TTY（minor device number of tty） STAT（状态） START（开始时间） TIME（执行时间） COMMAND（执行命令）
```

​    ps a 显示现行终端机下的所有程序，包括其他用户的程序。
​    ps -A 显示所有程序。
​    ps c 列出程序时，显示每个程序真正的指令名称，而不包含路径，参数或常驻服务的标示。
​    ps -e 此参数的效果和指定"A"参数相同。
​    ps e 列出程序时，显示每个程序所使用的环境变量。
​    ps f 用ASCII字符显示树状结构，表达程序间的相互关系。
​    ps -H 显示树状结构，表示程序间的相互关系。
​    ps -N 显示所有的程序，除了执行ps指令终端机下的程序之外。
​    ps s 采用程序信号的格式显示程序状况。
​    ps S 列出程序时，包括已中断的子程序资料。
​    ps -t<终端机编号> 指定终端机编号，并列出属于该终端机的程序的状况。
​    ps u 以用户为主的格式来显示程序状况。
​    ps x 显示所有程序，不以终端机来区分。

##[Linux查看物理CPU个数、核数、逻辑CPU个数](https://www.cnblogs.com/emanlee/p/3587571.html)

```shell
# 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
# 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

# 查看物理CPU个数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 查看每个物理CPU中core的个数(即核数)
cat /proc/cpuinfo| grep "cpu cores"| uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo| grep "processor"| wc -l
# 查看CPU信息（型号）
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```

##目录/文件占用空间

```shell
df -h # 查看各目录磁盘空间
# 查看目录大小并按照大小倒序展示  --max-depth=1：其中，数字“1”是指查询结果中最多显示的目录层数，这里指最多显示一层目录。
du -h --max-depth=1 path | sort -hr 
# 如 du -h --max-depth=1 /usr/local/ | sort -hr

# 用echo或cat或clear命令清除文件内容：
# 此命令会把/var/log目录中的big.log文件内容清除，而又不删除big.log文件
echo > /var/log/big.log 
# 此命令能与“echo > /var/log/big.log”达到相同效果，不过，命令执行后，需要用“Ctrl + d”结束
cat > /var/log/big.log 
# 此命令会把big.log文件内容清空，而不删除文件
clear > /var/log/big.log 
# 搜索当前目录下，超过800M大小的文件
find . -type f -size +800M 
# 查找超过800M大小文件，并显示查找出来文件的具体大小
find . -type f -size +800M  -print0 | xargs -0 du -h
```

> [linux磁盘空间占满问题快速定位并解决](https://www.cnblogs.com/nuccch/p/6797778.html)
>
> [centos磁盘爆满，查找大文件并清理](https://www.cnblogs.com/kluan/p/4458278.html)

##关机

- `shutdown`

    功能说明：系统关机指令。

    语　　法：`shutdown [-efFhknr][-t 秒数][时间][警告信息]`

    补充说明：shutdown指令可以关闭所有程序，并依用户的需要，进行重新开机或关机的动作。

     

    参　　数：

    `-c `　当执行"`shutdown -h 11:50`"指令时，只要按+键就可以中断关机的指令。

    `-f `　重新启动时不执行 fsck（磁盘维护）。

    `-F `　重新启动时执行 fsck。

    `-h `　将系统关机，关闭电源。如`shutdown -h now`,  `shutdown -h 10`

    `-k `　只是送出信息给所有用户，但不会实际关机。

    `-n` 　不调用init程序进行关机，而由shutdown自己进行。

    `-r` 　shutdown之後重新启动。

    `-t<秒数>` 　送出警告信息和删除信息之间要延迟多少秒。

    `[时间]`  设置多久时间後执行shutdown指令。

    `[警告信息] `　要传送给所有登入用户的信息。

- `halt` 相当于`shutdown -h`，其他参数` halt -help`

- `reboot` 关机并自动重启