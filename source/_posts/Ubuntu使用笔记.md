---
title: Ubuntu使用笔记
date: 2017-09-22 19:10:39
categories: linux配置
tags: linux,proxy,setting
description: 本文首发于http://liyongchun168.github.io,原文地址：http://liyongchun168.github.io/2017/09/22/linux下代理上网总结/,转载请注明署名“liyongchun”并在显眼位置保留原文连接，谢谢！
author: liyongchun
---

# 前言
linux下通过代理上网比较麻烦，各种应用都需要单独设置，现将各种情况汇总一下，方便自己也方便他人，这里以CentOS7.2为例进行介绍。

 
# 全局代理设置
打开profile文件
```bash
vim /etc/profile
```
添加如下内容：
```
http_proxy=http://username:password@yourproxy:808
https_proxy=https://username:password@yourproxy:808
export http_proxy
export http_proxy
```
# yum代理设置
打开yum的配置文件
```bash
vim /etc/yum.conf
```
添加如下内容：
```
proxy=http://username:password@yourproxy:808
```
# wget代理设置
打开wget的配置文件
```bash
vim ~/.wgetrc
```
添加如下内容：
```
https_proxy = http://username:password@yourproxy:808/
http_proxy = http://username:password@yourproxy:808/
ftp_proxy = http://username:password@yourproxy:808/
```
并将proxy打开
```
#If you do not want to use proxy at all, set this to off.
use_proxy = on
```

# git代理设置
打开git的配置文件
```bash
vim ~/.gitconfig
```
添加如下内容：
```
[http]
	proxy = http://username:password@yourproxy:808/
[https]
	proxy = https://username:password@yourproxy:808/
[url "https://github.com/"]
	insteadOf = git@github.com:
```


