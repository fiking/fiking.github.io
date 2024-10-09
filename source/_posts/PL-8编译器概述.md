---
title: PL_8编译器概述
tags:
  - compiler
categories:
  - dissertation translation
  - compiler
date: 2024-10-10 00:15:22
mathjax: true
description:
---

## 摘要

PL.8编译器接受多种源语言，并为几个不同机器生成高质量目标代码。使用的策略是先将源程序简单地转换成低级别中间语言。然后通过全局优化和寄存器分配来提高代码质量，而不是依赖于特殊代码选择。

 <!-- more -->

## 简介

PL.8是作为计算机架构，系统设计，编程语言和编译器技术之间交互的探索而开发的。

