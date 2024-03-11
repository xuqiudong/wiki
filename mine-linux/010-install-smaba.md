---
title: 010-ubunutu安装samba搭建局域网文件共享服务
description: ubunutu安装samba搭建局域网文件共享服务
published: true
date: 2024-03-11T02:28:01.467Z
tags: mine-linux, software, duoduo
editor: markdown
dateCreated: 2024-03-11T02:28:01.467Z
---

## 010.ubunutu安装samba搭建局域网文件共享服务

> 方便下载的一些动画片等在电视上给多多看。
>
> 所以搭建了一个文件共享服务，下载资源，存到服务器，然后在电视上播放

[参考：在手机上部署samba服务 ](https://www.jianshu.com/p/0ef03537b96a)

[转载自：【经验】Ubuntu samba的安装及使用方法](https://zhuanlan.zhihu.com/p/50753833)

**安装**

```text
sudo apt-get update
sudo apt-get install samba
```

**配置**

**指定账号的访问**

- 选择一个共享路径，假设为/home/work/sharedir，不存在的情况下可以自己创建

```text
sudo mkdir -p /home/work/sharedir
```

- 添加一个可访问用户到Ubuntu系统中，如smbuser，若存在则不需要创建

```text
sudo useradd smbuser -s /usr/sbin/nologin
```

- 修改文件权限使得smbuser用户能够访问共享路径

```text
sudo chown smbuser:smbuser /home/work/sharedir
```

- 将用户smbuser添加到samba的smbpasswd file中（即在samba服务中注册该账户）

```text
sudo smbpasswd -a smbuser
#后续设置登录密码，用于远程访问
```

- 修改samba配置文件（/etc/samba/smb.conf）

```text
# 打开文件
sudo vim /etc/samba/smb.conf
#在文件尾部添加以下信息，并保存（vim中:wq保存）
 
[secret]    #共享目录名，访问时的展示名
    comment = Secret File       #该共享目录的描述
    path = /home/work/sharedir  #访问的实际路径,前面设置的
    valid users = smbuser       #设置可访问的用户，此处为前面添加的用户smbuser（注意users不要拼写错误）
    guest ok = no               #是否允许访客，否
    writable = yes              #可写，是
    browsable = yes             #可浏览，是
```

- 重启服务，使上述设置生效

```text
sudo service smbd restart
sudo service nmbd restart
#或者以下方法
sudo restart smbd
sudo restart nmbd
```

**匿名访问**

> 匿名访问的设置和上述指定账号的类似

- 共享路径设置，此处选择的示例共享文件夹为/home/work/shareAll，若存在不需要再次创建

```text
sudo mkdir -p /home/work/shareAll
```

- 修改共享路径的权限(按需操作)

> 默认创建的路径权限是777 - $(umask)的结果,一般为只读权限

```text
#对目录的Others权限添加w(写)权限
sudo chmod o+w /home/work/shareAll
```

- 修改samba配置文件（/etc/samba/smb.conf）

```text
# 打开文件
sudo vim /etc/samba/smb.conf
# 尾部写入以下内容并保存
 
[share] 
    comment = Ubuntu File Server 
    path = /home/work/shareAll 
    browsable = yes 
    guest ok = yes 
    read only = no
```

- 重启服务

```text
sudo service smbd restart
sudo service nmbd restart
#或者以下方法
sudo restart smbd
sudo restart nmbd
```

**访问**

**mac访问**

- 在finder（访达，文件管理器）中用快捷键 cmd + k 打开链接对话框输
- 输入smb://IP（部署了samba服务的机器的ip地址）
- 选择访客，可以访问设置的匿名目录
- 选择用户，并输入对应的用户名(smbuser)密码，可访问指定账户的目录

**Linux访问（ubuntu示例）**

- 命令行挂载法，和挂载硬盘无本质差异

```text
sudo mount -t cifs //ip/username  local_dir -o user=xxx,passwd=xxx
# username是允许访问的账户此处可为smbuser
# local_dirs是挂载到本地的地址
# user=xxx指的是当前的用即user=smbuser
# passwd=xxx指的是用户smbuser配对的密码

# 解除挂载
sudo umount local_dir
```

- 图形界面手动加载法

在ubuntu的文件管理器的网络设备中添加该设备即可，[参考链接](https://link.zhihu.com/?target=https%3A//www.linuxidc.com/Linux/2017-11/148194.htm)



**windows访问**

- 调出运行 win+r 快捷键 （也可在文件管理器的地址栏中执行以下操作）
- 输入\\samba服务的地址
- 输入对应的账号密码（指定用户登录需要，匿名登录不需要）

> windows用户登录会存在一些问题（常见的是：windows无法访问),网上有一些解决方法，由于很少使用windows系统，没有过多了解处理方法。



**辅助命令**

```text
# 查看samba用户列表
pdbedit -L
 
# 对samba用户进行管理（用户已经在系统中创建）
smbpasswd -h  #查看支持的命令列表
 
# 异常时可查看日志情况
cat /var/log/samba/log.%m
```