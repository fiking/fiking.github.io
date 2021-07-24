---
title: git库创建和远程库关联
tags:
  - 总结文档
categories:
  - GIT
date: 2021-07-24 23:42:47
description: 
---

### 背景

  记录下本地创建git库，并关联远程库的过程和命令。远程库放在github上。

 <!-- more -->

### 主要过程和命令

#### 创建本地库

1. 创建库目录

   ```shell
   mkdir the_c++_Programming_language
   ```

2.  进入到上述目录，进行初始化git库

   ```
   git init
   ```

  实际执行的效果如下：

  {% if 1 == 1 %} 
      {% asset_img git_init.png title %}
  {% else %}
      ![](I:\Blogs\fiking\source\_posts\git库创建和远程库关联\git_init.png)

  {% endif %}

#### 创建github库

1. 点击new按钮进入创建页面
{% if 1 == 1 %} 
    {% asset_img new_repository.png title %}
{% else %}
    ![](I:\Blogs\fiking\source\_posts\git库创建和远程库关联\new_repository.png)

{% endif %}
2. 配置项目，然后点击create进行创建
{% if 1 == 1 %} 
    {% asset_img create_repo.png title %}
{% else %}
    ![](I:\Blogs\fiking\source\_posts\git库创建和远程库关联\create_repo.png)

{% endif %}

#### 关联远程库

##### 命令

  有两种协议可以访问。

1. https

   需要本地配置github用户名和密码。

   ```shell
   git remote remove https://github.com/fiking/the_cpp_Programming_language.git
   ```

   

2. ssh

   要上传ssh key到github。

   ```shell
   git remote add origin git@github.com:fiking/the_cpp_Programming_language.git
   ```

##### 错误处理
{% if 1 == 1 %} 
    {% asset_img error_refused.png title %}
{% else %}
    ![](I:\Blogs\fiking\source\_posts\git库创建和远程库关联\error_refused.png)

{% endif %}
没有配置https需要的用户名和密码，访问被拒绝。可以配置用户名和密码解决此问题。或者通过ssh进行绑定。
