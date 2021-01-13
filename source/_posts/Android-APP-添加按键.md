---
title: Android APP 添加按键
tags:
  - Andorid App
categories:
  - Android
description: 
date: 2021-01-08 08:40:47
---

  初级常用的代码之按键添加。

 <!-- more -->

  首先在布局中把按键控件加入界面中，设定ID等属性。然后定义一个Button变量来获取绑定按键控件。

  主要一个问题是按键控件资源是在其所在的activity资源已经实例化后才能获取到，否则会有空对象错误。及如下代码顺序：

  ```java
setContentView(R.layout.activity_main);
 
// findViewById must be after setContentView
button = findViewById(R.id.button_f);
  ```

### 参考

[attempt-to-invoke-virtual-method](https://stackoverflow.com/questions/36666091/attempt-to-invoke-virtual-method-android-view-windowcallback-android-view-wind)







