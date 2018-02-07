---
title: initrd中添加驱动
date: 2017-11-11 15:10:39
categories: linux配置
tags: linux,driver,initrd
description: 本文首发于http://liyongchun168.github.io,原文地址：http://liyongchun168.github.io/,转载请注明署名“liyongchun”并在显眼位置保留原文连接，谢谢！
author: liyongchun 
---

# 前言
通过向initrd中添加驱动，实现旧的linux系统对新的硬件的支持，本文以CentOS7.2为例。
# 解压initrd.img文件

```bash
xz -dc initrd.img | cpio -id
```
# 压缩initrd.img文件
```bash
find . | cpio -c -o | xz -9 --format=lzma > ../initrd.img
```

