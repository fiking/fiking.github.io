---
title: V8-early 之shell实现
tags:
  - v8
categories:
  - js engine
  - v8
date: 2021-08-15 20:36:22
description:
---

## 前言

  0.1.5 版本的 V8 代码只是编译成静态库，缺少 shell 程序，不利与调试。本文档尝试在这个版本中加入 shell，实现JS文件的执行。

 <!-- more -->

## 基本方案

  参考0.2.5版本的sample进行添加。分为：

1. 添加新文件，实现 main 函数 
2. Scon 脚本配置
3. 实现JS文件读取
4. JS文件解析
5. JS文件执行

## 实现

按上述的方案进行代码实现。

### 添加新文件

````shell
diff --git a/src/shell.cc b/src/shell.cc
new file mode 100644
index 0000000..1934e48
--- /dev/null
+++ b/src/shell.cc
@@ -0,0 +1,8 @@
+#include <cstdio>
+
+int main(int argc, char *argv[]) {
+  printf("Hello shell!\n");
+  return 0;
+}
````

### 脚本配置

简单方案参考 mksnapshot，配置脚本生成shell可执行文件。故把文件放在src下。后续可以参考0.2版本，单独放在sample目录中。

```c++
diff --git a/src/SConscript b/src/SConscript
index 3c04ec1..2c0f156 100644
--- a/src/SConscript
+++ b/src/SConscript
@@ -379,6 +379,10 @@ def ConfigureBuild():
   else:
     snapshot_obj = empty_snapshot_obj

+  # Create shell
+  shell_src = 'shell.cc'
+  shell = env.Program('shell', [shell_src, libraries_obj, non_snapshot_files, empty_snapshot_obj], PDB='shell.exe.pdb')
+
   all_files = [non_snapshot_files, libraries_obj, snapshot_obj]
   if library_type == 'static':
     env.StaticLibrary('v8', all_files)
```

### 文件读取



## 参考

