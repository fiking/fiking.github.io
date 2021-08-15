---
title: ubuntu-20 配置
tags:
  - ubuntu
categories:
  - ubuntu
date: 2021-08-13 21:37:13
description:
---

## 背景

  ubuntu 20 初始使用的一些配置和安装命令。

 <!-- more -->

## 软件安装

### SSH Server

1. 安装

   ````shell
   sudo apt install openssh-server
   ````

2. 查看 ssh server 的状态

   ```shell
   systemctl status sshd
   ```

### proxy 配置

  使用 shadowsocks 进行代理，要在本地安装client。

1. 安装client

   ```shell
   sudo apt install python3-pip
   sudo pip install git+https://github.com/shadowsocks/shadowsocks.git@master
   ```

2. 配置文件

   添加 /etc/shadowsocks.json 文件

   ```shell
   {
       "server":"服务器 IP 或是域名",
       "server_port":端口号,
       "local_address": "127.0.0.1",
       "local_port":1080,
       "password":"密码",
       "timeout":300,
       "method":"加密方式 (chacha20-ietf-poly1305 / aes-256-cfb)",
       "fast_open": false
   }
   ```

3. 启动服务

   ```shell
   sudo /usr/local/bin/sslocal -c /etc/shadowsocks.json -d start
   ```

4. 配置全局使用

   ```shell
   sudo apt install privoxy
   sudo vim /etc/privoxy/config
   systemctl restart privoxy
   ```

   参考 [https://www.wylu.me/posts/eed37a90/](https://www.wylu.me/posts/eed37a90/) 的 “配置 Ubuntu 使用 Global 模式”。

## 配置下载源

1. 编辑 /etc/apt/sources.list

   ```c++
   # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
   
   # 预发布软件源，不建议启用
   # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
   ```

2. 执行命令

   ````shell
   sudo apt-get update
   sudo apt-get upgrade
   ````

   

## 参考

### ssh

[https://linuxconfig.org/ubuntu-20-04-ssh-server](https://linuxconfig.org/ubuntu-20-04-ssh-server)

### proxy

[https://www.wylu.me/posts/eed37a90/](https://www.wylu.me/posts/eed37a90/)

[https://shadowsockshelp.github.io/Shadowsocks/linux.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF](https://shadowsockshelp.github.io/Shadowsocks/linux.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF)

[https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)

[https://cjh.zone/2018/11/25/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-Linux-Ubuntu-16-04-%E9%85%8D%E7%BD%AEshadowsocks%E5%AE%A2%E6%88%B7%E7%AB%AF/](https://cjh.zone/2018/11/25/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-Linux-Ubuntu-16-04-%E9%85%8D%E7%BD%AEshadowsocks%E5%AE%A2%E6%88%B7%E7%AB%AF/)

### 下载源配置

[https://blog.csdn.net/xiangxianghehe/article/details/105688062](https://blog.csdn.net/xiangxianghehe/article/details/105688062)

