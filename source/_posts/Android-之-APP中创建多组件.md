---
title: Android 之 APP中创建多组件
tags:
  - Android App
categories:
  - Android
description: 
date: 2021-01-08 08:40:47
---

在APP中创建新的组件需要在AndroidManifest.xml中注册，否则会出现异常，如下注册一个Activity：

 <!-- more -->

```xml
    <application
        <activity android:name=".SecondActivity">
        </activity>
    </application>
```

### 参考

[android-error-unable-to-find-explicit-activity-class](https://stackoverflow.com/questions/8107789/android-error-unable-to-find-explicit-activity-class)





