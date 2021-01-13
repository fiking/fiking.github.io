---
title: gpio学习
tags:
  - Linux driver
categories:
  - Linux
description: 
date: 2021-01-08 08:40:47
---

  主要的资料是kernel自带的Document/gpio 目录下的文档。

 <!-- more -->

  因为gpio的接口标准有新旧两套，故文件也可以大致分为两部分。

1. 新的gpio接口标准（the descriptor-based interface）
   - gpio.txt ： 总的介绍gpio的概念和gpio的主要属性。
   
   - driver.txt ：描述如何编写一个gpio 控制器的驱动。
   
   - board.txt  : 描述某个设备获取gpio资源的三种方式（DT ACPI platform_data）。
   
   - consumer.txt : 描述某个设备驱动中控制gpio的常用API。
   
   - drivers-on-gpio.txt ：gpio常用的使用实例。
   
   - sysfs.txt ：给用户空间提供直接控制gpio的方法。
   
     
2. 旧版gpio接口（the legacy integer-based interface）
   
   - gpio-legacy.txt  ：介绍旧版gpio的使用方式。