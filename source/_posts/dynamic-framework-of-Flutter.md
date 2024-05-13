---
title: Flutter动态化更新
tags:
  - Flutter
categories:
  - cross platform
date: 2024-05-12 20:31:58
description:
---

## 简介

Flutter 是 Google 开源的跨平台应用开发框架，使用这个框架开发的应用有两种执行方式。

- JIT：用于开发阶段，可以热更新Dart源文件，改变UI布局，便于应用开发。
- AOT：用于发布阶段，Dart源文件被直接编译成目标机器码，无法进行热更新。

<!-- more -->

因为发布阶段的Flutter应用无法进行热更新，所以Flutter应用每次更新都必须重新发布版本， 提交新的安装包到应用市场进行审核。审核通过后，用户在应用市场进行App的下载更新。对于应用开发者和用户，这种版本更新方式存在以下问题[1]。

1. 审核周期长， 且可能审核不通过。 周期长导致发版本不够灵活， 紧急的业务需求不能及时上线。

2. 线上出现急需修复的bug时，需要较长修复周期，影响用户体验。

3. 安装包过大， 动辄几十兆几百兆的应用升级可能会让用户比较抗拒。

4. 即使上线了，也无法达到全部用户升级， 服务端存在兼容多版本App的问题。

为了解决这些问题，需要增加Flutter应用动态化更新的能力，实现以下功能。

1. 随时实现功能升级，不存在应用市场长时间审核和拒绝上线问题，达到业务需求快速上线的目的。
2. 线上bug可以实时修复，提高用户体验。
3. 可以减小发版功能包体积，只需要替换新增功能即可。
4. 功能保持一致，类似网页一样，发版后用户同步更新，不存在旧版本兼容问题。

由于Flutter官方还不支持应用动态化更新，因此，应用开发厂商自行基于Flutter框架扩展了Flutter应用动态化更新的功能。

## 动态化方案

基于Flutter应用框架的现状，业界探索了一些动态化方案。

| 方案         | 简介                                                         |
| ------------ | ------------------------------------------------------------ |
| 产物替换     | 官方曾经推出了 Code Push 方案，但对动态化后的性能没有自信，并且对安全性有所顾虑。 |
| AOT 搭载 JIT | 在Release模式下引入支持JIT的DartVM，存在应用包过大的问题，且不支持IOS设备。 |
| 动态生产 DSL | 通过JS动态产生Flutter渲染所需的UI信息，存在JS和Dart交互的问题，性能开销大。 |
| 静态产生DSL  | 离线产生Flutter渲染所需的UI信息。                            |

目前有三个主要的Flutter应用动态更新项目：

| 项目      | 开发者 | 核心思想                                  | 开发语言 | 优势                                                 | 劣势                         | 现状                       |
| --------- | ------ | ----------------------------------------- | -------- | ---------------------------------------------------- | ---------------------------- | -------------------------- |
| MXFlutter | 腾讯   | 用js编写Dart，动态拉取js脚本              | JS       | 目前相对最完整的Flutter使用JS开发方案                | 采用js方式编写Dart，维护困难 | 代码仓3年前停更了          |
| MTFlutter | 美团   | 布局，逻辑都使用Dart,增加语法解析和运行时 | Dart     | 支持布局动态化和逻辑动态化                           | 未开源                       | 未知（官方blog是2020年的） |
| Fair      | 58     | 通过bundle和js实现热更新                  | Dart     | 支持布局动态化和逻辑动态化开源社区活跃, 开发工具丰富 | 部分语法不支持               | 代码仓微弱更新中           |

### MXFlutter



## 参考

1. [Flutter动态化调研实践 - 掘金 (juejin.cn)](https://juejin.cn/post/7174978087879671865)
2. [wuba/Fair: A Flutter package used to update widget tree dynamically. Fair提供一整套Flutter动态化解决方案 (github.com)](https://github.com/wuba/fair)
3. [美团外卖Flutter动态化实践 - 美团技术团队 (meituan.com)](https://tech.meituan.com/2020/06/23/meituan-flutter-flap.html)
4. [基于JS的高性能Flutter动态化框架MXFlutter-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1472361)
5. [MXFlutter/Documentation/基于JS的高性能动态化框架详细介绍.md at master · chenzhengxu/MXFlutter (github.com)](https://github.com/chenzhengxu/MXFlutter/blob/master/Documentation/基于JS的高性能动态化框架详细介绍.md)



