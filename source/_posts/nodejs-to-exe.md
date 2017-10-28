---
title: 如何将nodejs制作的控制台程序打包成exe
date: 2017-06-22 11:27:53
categories: Node.js
tags: nodejs,exe
description: 本文首发于 http://liuxianan.com，原文地址：http://blog.liuxianan.com/nodejs-to-exe.html，转载请注明署名“liuxianan”并在显眼位置保留原文链接，谢谢！
author: liuxianan
---

# 前言

虽然控制台程序本来就是没有界面的，打包成exe有点多余，但是如果想把写好的代码发给一个非程序员使用，可能还要教人家如何装node，如何运行…… 麻烦死了！

本文采用的方法是先用批处理包装，然后再将批处理打包成exe，批处理转exe的工具很多，我这里随便找了一个`Bat To Exe Converter`来实现。

# 正式开始

## 目标

我们肯定希望生成的exe是这样的：

1. 双击即可运行，临时文件不能生成在当前目录（这样看起来才像个独立的exe）；
2. 退出程序时自动删除临时文件（不然时间越久临时目录越大）；
3. 可以任意设置图标；
4. 虽然文件输出到临时目录，但是工作目录最好是exe的启动目录；

上面1、2、3这三点都比较好实现，关键是第4点。

## 测试文件

我们写一个最简单的控制台程序，只为了验证当前目录，`test.js`：

```javascript
console.log('当前工作目录：' + process.cwd());
```

## 批处理封装

批处理可以设置窗口标题以及字体颜色等，所以我们用批处理再包装一层：

```bash
@echo off
node test.js
pause
```

假设以上文件在`D:\test`下面，我们在`E:\exe`下面执行`call D:\test\test.bat`，很显然，由于批处理的默认当前目录在E盘，所以报错：

```bash
E:\exe>call D:\test\test.bat
module.js:471
    throw err;
    ^

Error: Cannot find module 'E:\exe\test.js'
    at Function.Module._resolveFilename (module.js:469:15)
    at Function.Module._load (module.js:417:25)
    at Module.runMain (module.js:604:10)
    at run (bootstrap_node.js:394:7)
    at startup (bootstrap_node.js:149:9)
    at bootstrap_node.js:509:3
```

所以我们把批处理改成这样（同时为了后面测试方便，我们多输出了2个参数）：

```bash
@echo off
echo %cd%
echo %~dpf0
node %~dp0%test.js
pause
```

运行之后正常：

```bash
E:\exe>call D:\test\test.bat
E:\exe
D:\test\test.bat
当前工作目录：E:\exe
请按任意键继续. . .
```

## 尝试使用好压打包成exe

使用好压的自解压模式可以轻松将批处理打包成exe（下图为900多kb的gif，耐心等待加载）：

![使用好压打包exe](http://image.liuxianan.com/201707/20170727_163238_518_3340.gif)

只可惜打包之后，它的工作目录默认是解压的临时目录：

```bash
当前工作目录：C:\Users\Administrator\AppData\Local\Temp\HZ$D.816.1632
请按任意键继续. . .
```

尝试了一番还是没找到解决办法，所以只能放弃了，如果有人知道还烦请告知。

## 使用第三方工具

我使用了一个叫[Bat To Exe Converter](http://www.pc6.com/softview/SoftView_31106.html)的工具：

![](http://image.liuxianan.com/201707/20170727_151345_112_3422.png)

![](http://image.liuxianan.com/201707/20170727_151402_027_8982.png)

![](http://image.liuxianan.com/201707/20170727_151414_892_9578.png)

配置好以上几步之后，点击左下角的编译就OK了：

![](http://image.liuxianan.com/201707/20170727_160359_291_3179.png)

为了对比，我们把双击生成的test.exe也移到`E:\exe`下面测试：

![](http://image.liuxianan.com/201707/20170727_154210_243_1912.png)

可以发现，这个工具把`%cd%`和`%~dpf0`和普通方式对调了一下，工作目录是临时目录，而`%0`却成了启动exe的路径，好吧，这样也没关系，我们这样改造一番就OK了：

```bash
@echo off
:: 将当前目录保存起来
set tempPath=%cd%
:: 定位到exe所在目录
%~d0
cd %~dp0
:: 执行临时目录的js文件
node %tempPath%\test.js
pause

```

![](http://image.liuxianan.com/201707/20170727_155429_501_8294.png)

至此，我们完美地解决了最开始说的第4点问题^_^。

# 实例：nginx配置文件监听工具

别整些没用的，我们来点实际的。实际工作中经常需要改动nginx配置文件，每次改完都需要reload一下感觉很麻烦，于是简单写了个配置文件监听工具，一改动就自动刷新，代码如下：

nginx-conf-watcher.js：

```js
const fs = requir