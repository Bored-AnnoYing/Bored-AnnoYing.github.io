---
title: Ubuntu16.04搭建SVN服务
date: '2018/8/02 19:21:20'
updated: '2018/9/25 22:28:52'
tags:
  - Ubuntu
  - svn
---
>参考文档：
[Ubuntu下安装SVN及配置：](https://www.cnblogs.com/oneTOinf/p/7927781.html)https://www.cnblogs.com/oneTOinf/p/7927781.html
[SVN服务器在Ubuntu16.04下搭建多版本库详细教程：](https://www.cnblogs.com/lovechen/p/5716213.html)https://www.cnblogs.com/lovechen/p/5716213.html
## 一、安装
### 直接使用命令进行安装

<!-- more -->

```shell
sudo apt-get update
sudo apt-get install subversion
```

## 二、配置
### 1.在home下创建一个svn文件夹

```shell
sudo mkdir  /home/svn
# 第一次搭建没有赋予权限，客户端访问服务器被拒绝
sudo chmod -R 777 /home/svn/
```

### 2.创建仓库
```shell
sudo mkdir /home/svn/HairRobot
sudo svnadmin create /home/svn/HairRobot
```
进入到仓库中使用ls命令查看
```shell
cd /home/svn/HairRobot
ls
```
![HairRobot](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884202624.png)
### 3.为了便于管理，将所有版本库的密码和权限设置在同一个文件下面，操作步骤如下
取出HairRobot下面conf文件夹下的authz和passwd两个文件到svn根目录下面
```shell
sudo cp /home/svn/HairRobot/conf/authz /home/svn/authz
sudo cp /home/svn/HairRobot/conf/passwd /home/svn/passwd
```

### 4.修改HairRobot仓库库目录conf文件夹下面的svnserve.conf文件
```shell
cd /home/svn/HairRobot/conf
sudo vi svnserve.conf
[general]
#匿名用户不可读
anon-access = none
#权限用户可写
auth-access = write
#密码文件为passwd
password-db = ../../passwd
#权限文件为authz
authz-db = ../../authz
```
`password-db = ../../passwd，authz-db = ../../authz`代表相对路径而非绝对路径

![svnserve.conf](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884242459.png)

### 5.添加角色（密码数据库）
打开svn目录下的passwd文件，创建方法是在[user]下面添加 username = passwd，记得“=”前后的空格
```shell
cd /home/svn
sudo vi passwd
```
![passwd](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884284029.png)

### 6.添加组及分配权限（认证规则库）
```shell
cd /home/svn
sudo vi authz
```
[groups] section：为了便于管理，可以将一些用户放到一个组里边，比如：owner=allen,ellen
groups下边的sections表示对一个目录的认证规则，比如对根目录的认证规则的section为[/]。设置单用户的认证规则时一个用户一行
```shell
[/]
whoami = rw  # whoami 对根目录的权限为rw
test   = r   # test 对根目录的权限为r
```
如果使用group，需要在group名字前加@,如

```shell
@owner=rw  #group owner中的用户均为rw，等价于上边的两句话
```
启动时如果从/home/svn/HairRobot启动，/就是HairRobot目录，用如上方式以HairRobot目录为根设置权限。

如果从/home/.svn/启动，每个仓库根还是自己的起始目录。可以采用如上方式设置HairRobot的权限，也可以采用如下方式：

`*`代表所有人有r（读权限）

```shell
[HairRobot:/]
@owner=rw
*  = r
```
每个仓库的根目录(/)就是自己的起始目录；[repos:/]这种方式只适用于多仓库的情况；[/]适合于单仓库和单仓库的方式。

### 7.启动和停止
svnserve -d -r 目录 --listen-port 端口号
```shell
svnserve -d -r /home/svn
```
访问方式：`svn://172.21.24.6/HairRobot`

停止服务
```shell
killall svnserve
```

### 8.检查
`netstat -an | grep 3690`可以检查服务是否启动
```shell
netstat -an | grep 3690
tcp        0      0 0.0.0.0:3690            0.0.0.0:*               LISTEN   
```

## 三、自启动
### 1.在/etc/init.d目录建立一个脚本文件svnd.sh
```shell
cd /etc/init.d
sudo touch svnd.sh
sudo chmod +x svnd.sh
sudo vi svnd.sh
```

### 2.svnd.sh文件中添加如下内容
```shell
#!/bin/bash
### BEGIN INIT INFO
# Provides:          bbzhh.com
# Required-Start:    $local_fs $network
# Required-Stop:     $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: tomcat service
# Description:       tomcat service daemon
### END INIT INFO
#svnserve startup
svnserve -d -r /home/svn
```
ubuntu 16.04中一定要加上以下LSB信息，不然放入启动脚本的时候会报错无法开机启动

![LSB](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884308558.png)

### 3.更新修改权限
```shell
update-rc.d svnd.sh defaults
```