---
title: linux系统分类
date: 2018-02-27 16:07:34
tags:
---
一般来说著名的linux系统基本上分两大类：
* RedHat系列：Redhat、Centos、Fedora等
* Debian系列：Debian、Ubuntu等

#### RedHat系列 

常见的安装包格式 rpm包,安装rpm包的命令是“rpm -参数” 

包管理工具 yum 

```
yum的配置文件：/etc/yum.conf 
yum install gcc  [centos] 
更新：yum update 
安装：yum install xxx 
移除：yum remove xxx 
清除已经安装过的档案（/var/cache/yum/）：yum clean all 
搜寻：yum search xxx 
列出所有档案：yum list 
查询档案讯息：yum info xxx 
```
支持tar包

#### Debian系列 

常见的安装包格式 deb包,安装deb包的命令是“dpkg -参数” 

包管理工具 apt-get 

```
更新软件包：apt-get update
安装：apt-get install xxx
移除：apt-get remove xxx
更新安装过的包：apt-get upgrade xxx
```
支持tar包
