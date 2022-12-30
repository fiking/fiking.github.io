---
title: LLVM 环境搭建
tags:
  - llvm
categories:
  - 实践
  - compiler
  - llvm
date: 2022-09-28 22:31:33
description:
---

## 背景

为了学习了解 LLVM 的代码，从其官方仓库中下载代码，然后进行配置，生成可执行文件，便于学习代码过程中调试使用。

 <!-- more -->

## 编译环境

我们在 CPU 是 x86_64 ，操作系统为 Linux 的环境下编译 LLVM，具体版本信息如下：

- 机器环境：x86_64
- 操作系统：Ubuntu 20.04.2 LTS
- LLVM：13.0.1

## 主要过程

### 下载代码

从LLVM 在GitHub上的代码仓下载：

```shell
git clone https://github.com/llvm/llvm-project.git
```

### 编译

llvm 编译比较简单，可以通过宏配置可选的部件（Clang等），设置编译类型和目标平台[2]。

```shell
#!/bin/bash

mkdir -p build
cd build

if [ ! -f bin/llvm-lto ]; then
  CORE_NUM=$(nproc --all)
  if [ -z "${CORE_NUM}" ]; then
    CORE_NUM=1
  fi

  echo "Build llvm with" ${CORE_NUM} "cores"

  cmake /mnt/Compiler/llvm-project/llvm \
    -G "Unix Makefiles" \
    -DCMAKE_BUILD_TYPE="Debug" \
    -DLLVM_TARGETS_TO_BUILD="X86;AArch64;BPF;Hexagon" \
    -DLLVM_ENABLE_PROJECTS="clang" \
    -DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi"

  cmake --build .
else
  echo "llvm has already been built"
fi
```

## 参考

1. [虚拟机编译符号链接不允许](https://www.cnblogs.com/pxl0/p/5899626.html)

2. [Building LLVM](https://llvm.org/docs/GettingStarted.html#id4)