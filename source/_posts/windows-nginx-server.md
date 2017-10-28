---
title: Windows下将nginx配置成服务并设置开机自启动
date: 2017-05-27 11:38:01
categories: nginx
tags: nginx,windows,server
description: 本文首发于 http://liuxianan.com，原文地址：http://blog.liuxianan.com/windows-nginx-server.html，转载请注明署名“liuxianan”并在显眼位置保留原文链接，谢谢！
author: liuxianan
---

# 概述

一般有2种方法，推荐第二种。

# 方法一(创建服务)

我们使用`Windows Service Wrapper`来为nginx创建服务，为什么不使用`instsrv/servany`、`FireDaemon`或者其他办法呢？

下面是网上摘录的一段话：

> 通过 instsrv/srvany(微软官方创建服务的方法) 或者 FireDaemon 的方式(来创建 Nginx 为服务)，只是启动进程，当你想要停止它时，将关闭这个进程。但这些方式都无法关闭多余的那个 nginx.exe 进程。所以每次你停止/启动/重启服务都会产生一个多余的 nginx.exe 进程，不方便！

从网上下载一个`Windows Service Wrapper(winsw.exe)`，然后复制到nginx根目录，可以随意改名字，比如我改成`nginx-server.exe`，然后新建一个xml文件（文件名比如和exe同名，比如我的叫`nginx-server.xml`），内容如下：

```html
<?xml version="1.0" encoding="UTF-8" ?>
<service>
	<id>nginx</id>
	<name>nginx</name>
	<description>nginx server</description>
	<executable>D:\GreenSoft\nginx-1.11.8\nginx.exe</executable>
	<logpath>D:\GreenSoft\nginx-1.11.8\server-logs\</logpath>
	<logmode>roll</logmode>
	<depend></depend>
	<startargument>-p D:\GreenSoft\nginx-1.11.8</startargument>
	<stopargument>-p D:\GreenSoft\nginx-1.11.8 -s stop</stopargument>
</service>
```

![](http://image.liu