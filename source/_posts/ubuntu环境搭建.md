---
title: ubuntu环境搭建
date: '2018/7/31 13:33:32'
updated: '2018/9/25 22:28:13'
tags:
  - linux
  - Ubuntu
---
### 设置默认密码
```shell
sudo passwd
[sudo] password for you ：---> 输入你的密码（你现在这个用户的密码），不回显
Enter new UNIX password: --- > 设置root 密码
Retype new UNIX password: ---> 重复密码
```

<!-- more -->

### 更新源列表
[源列表](http://wiki.ubuntu.org.cn/%E6%BA%90%E5%88%97%E8%A1%A8)：http://wiki.ubuntu.org.cn/%E6%BA%90%E5%88%97%E8%A1%A8
sources.list系统自带的，源是来Ubuntu的官网！安装包比较慢，所以最好切换成国内的
```shell
cd /etc/apt
sudo cp sources.list sources.list.bak
sudo vi sources.list


# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
```
最后执行`sudo apt-get update`更新源


### xshell访问,安装SSH
```shell
sudo apt-get install openssh-server
```

### sftp连接，安装vsftpd
```shell
sudo apt-get install vsftpd
```

### deb的安装与卸载
[原文：Ubuntu下的deb打包、安装与卸载](https://blog.csdn.net/hebbely/article/details/78521476?locationNum=5&fps=1)
```shell
sudo dpkg -i mysql-apt-config_0.8.10-1_all.deb
sudo dpkg -r mysql-apt-config
```

### ubuntu16.04 dpkg错误：另外一个进程已为dpkg状态库加锁
重启或执行命令
```shell
sudo rm /var/lib/dpkg/lock
```