---
title: Linux Pstore 简介
tags:
  - Linux Pstore
categories:
  - Linux
description:
date: 2021-01-08 08:40:47
---

  Pstore ： persistent storage

 <!-- more -->

  主要用于存储内核异常时的log信息。实现方式是，管理一块“非易失性的存储空间”，如不断电的RAM或外部存储，当系统异常时，将log信息写到Pstore管理的存储空间，直到下一次系统正常时，在将log读出来，以文件形式提供给用户使用。

  模块代码主要在 kernel/fs/pstore 下。

  由四部分组成：

-   一个核心组件：pstore

  源码：platform.c inode.c

- 三个功能组件：

  ftrace   : ftrace.c

  pmsg   : pmsg.c

  ramoops: ram_core.c

- 注册ramoops驱动：ram.c

- 注册ramoops设备：客制化

###  参考文档：

[](kernel/Documentation/ramoops.txt )

[](http://blog.csdn.net/zangdongming/article/details/37729315)

[](http://blog.51cto.com/xiamachao/1872790)

[]( Documentation/ABI/testing/pstore)

[](http://blog.csdn.net/ldinvicible/article/details/51789041)






