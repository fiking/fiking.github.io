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

### Samba 配置

为了实现 Win10 和 Ubuntu 20  的文件共享，设置了 Samba 服务。

```shell
# 安装软件
sudo apt isntall samba samba-common

# 添加用户密码
sudo smbpasswd -a {username}
```

编写配置文件

```shell
# 打开配置文件
sudo vim /etc/samba/smb.conf
# 在配置文件smb.conf最后添加下面的内容（[]中是待共享目录的文件名，path是带共享目录的绝对路径）
[share]
comment = share folder
browseable = yes
path = /home/{username}/share
create mask = 0700
directory mask = 0700
valid users = {username}
public = yes
available = yes
writable = yes
```

重启

```shell
sudo service smbd restart
```

## 内存配置

### 扩大 swap 分区

```c++
# 将现有swap移动到主内存，可能需要几分钟
sudo swapoff -a

# 创建新的swap文件，bs×count=最后生成的swap大小，这里设置8G
sudo dd if=/dev/zero of=/swapfile bs=1G count=8

# 设置权限
sudo chmod 0600 /swapfile

# 设置swap
sudo mkswap /swapfile

# 打开swap
sudo swapon /swapfile

# 检查设置是否有效
grep Swap /proc/meminfo # 或者htop看一下

# 设置永久有效
sudo gedit /etc/fstab
# 在末尾行加上 
# /swapfile swap swap sw 0 0
```

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



## 环境异常解决

### 网络异常

虚拟机下，启动 ubuntu-20 后发现无网络连接，状态栏的网络图标也缺失，但vmware虚拟机网络配置是正常的。查找原因发现可以通过以下命令重启网络服务，修复这个问题[vmware1]：

```shell
sudo service network-manager stop

sudo rm /var/lib/NetworkManager/NetworkManager.state

sudo service network-manager start

sudo vim /etc/NetworkManager/NetworkManager.conf    #把第四行的false改成true（managed=false改为：managed=true）注:也有可能在第五行

sudo service network-manager restart
```

### 下载证书错误问题

通过如下 -o 选项可以临时关闭证书验证。

```
apt-get -o Acquire::https::Verify-Peer=false update
```



## 共享文件夹

解决虚拟机共享文件夹看不见的问题[vmware2]：

```shell
// 查看vmware 共享是否设置成功
vmware-hgfsclient
// 手动挂载文件夹
sudo vmhgfs-fuse .host:/ /mnt -o nonempty -o allow_other
```



## 参考

### ssh

1. [https://linuxconfig.org/ubuntu-20-04-ssh-server](https://linuxconfig.org/ubuntu-20-04-ssh-server)

### proxy

1. [https://www.wylu.me/posts/eed37a90/](https://www.wylu.me/posts/eed37a90/)

2. [https://shadowsockshelp.github.io/Shadowsocks/linux.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF](https://shadowsockshelp.github.io/Shadowsocks/linux.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF)

3. [https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)

4. [https://cjh.zone/2018/11/25/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-Linux-Ubuntu-16-04-%E9%85%8D%E7%BD%AEshadowsocks%E5%AE%A2%E6%88%B7%E7%AB%AF/](https://cjh.zone/2018/11/25/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-Linux-Ubuntu-16-04-%E9%85%8D%E7%BD%AEshadowsocks%E5%AE%A2%E6%88%B7%E7%AB%AF/)

### vmware

1. [VMware Workstation Ubuntu 20.04 无法连接网络问题](https://blog.51cto.com/u_15346769/3688228)
2. [VMware共享文件夹——Ubuntu20.04](https://blog.csdn.net/weixin_44126785/article/details/120585921)

### 下载源配置

[https://blog.csdn.net/xiangxianghehe/article/details/105688062](https://blog.csdn.net/xiangxianghehe/article/details/105688062)

[https://juejin.cn/post/7033412379727626247](https://juejin.cn/post/7033412379727626247)

### 下载证书错误

[https://github.com/IntelRealSense/librealsense/issues/10980#issuecomment-1272884516](https://github.com/IntelRealSense/librealsense/issues/10980#issuecomment-1272884516)

### samba 挂载

https://developer.huawei.com/consumer/cn/forum/topic/0202827366574480034?fid=0103702273237520029

### 扩大 swap 分区

[https://zhuanlan.zhihu.com/p/480903179](https://zhuanlan.zhihu.com/p/480903179)

### VSCode 跳转配置

[https://blog.csdn.net/weixin_43083491/article/details/119573501](https://blog.csdn.net/weixin_43083491/article/details/119573501)

### 命令行配置

[https://forum.ubuntu.com.cn/viewtopic.php?t=466064](https://forum.ubuntu.com.cn/viewtopic.php?t=466064)

### wsl 配置

https://alessandrococco.com/2021/01/wsl-how-to-resolve-operation-not-permitted-error-on-cloning-a-git-repository

https://zhuanlan.zhihu.com/p/466001838

### 挂载磁盘

https://juejin.cn/post/6844903730068865038

### 磁盘扩展

https://www.jianshu.com/p/13f59261e343









