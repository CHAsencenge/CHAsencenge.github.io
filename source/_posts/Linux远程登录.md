---
title: Linux远程登录及服务器配置
date: 2019-07-17 21:45:43
tags:
- 远程登录
categories:
- Linux
---
<blockquote class= "blockquote-center">Linux远程登录及服务器配置</blockquote>
<!--more-->

##Linux远程登录原理
安装telnet远程客户端登录工具的客户机通过23（默认）端口连接到Linux服务器，通过telnet将远程客户机上命令传到服务器执行并反馈。
###telnet软件包安装
```sudo apt-get install inetutils-telnetd```(ubuntu没有默认安装Telnet软件包)
