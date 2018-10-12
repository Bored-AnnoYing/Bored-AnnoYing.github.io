---
title: Ubuntu16.04远程控制
date: '2018/7/31 14:42:44'
updated: '2018/9/25 22:28:32'
tags:
  - Ubuntu
  - 远程控制
---
### 一、设置允许桌面控制

#### 1.系统-》首选项-》桌面共享

![zhuo](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884007624.png)

<!-- more -->

#### 2.设置桌面共享
将【允许其他人查看您的桌面】这一项勾上，然后在安全那项，勾选【要求远程用户输入此密码】，并设置远程密码。并且我们取消勾选【必须为对本机器的每次访问进行确定】（这样做，是为了被远程的时候不需要再确认，否则每次远程都要人为确认才能被远程，会很繁琐）如图所示：

![设置](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884007694.png)

### 二、安装软件

#### 1.安装xrdp vnc4server tightvncserver
```shell
sudo apt-get install xrdp

sudo apt-get install vnc4server

sudo apt-get install tightvncserver
```

#### 2.安装xubuntu-desktop
```shell
sudo apt-get install xubuntu-desktop
```

#### 3.向xsession中写入xfce4-session
```shell
echo “xfce4-session” >~/.xsession
```

#### 4.开启xrdp服务
```shell
sudo service xrdp restart
```

### 三、安装dconf-editor
```shell
sudo apt-get install dconf-editor

dconf-editor
```
打开之后，依次展开org->gnome->desktop->remote-access，然后取消 “requlre-encryption”的勾选即可。如图所示：

![dconf-editor](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884007708.png)

### 四、远程连接

#### 1.打开远程连接

![mstsc](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884007622.png)

#### 2.输入用户名密码

![login to xrdp](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884007623.png)

#### 3.登录成功

![登录成功](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884008142.png)

### 五、可能遇到的问题

#### 1.xrdp 访问 ubuntu 只看到背景，看不到菜单栏
安装gnome-pane
```shell
sudo apt-get install gnome-panel
```

#### 2.出现错误：连接错误
错误信息如下：
```shell
connecting to sesman ip 127.0.0.1 port 3350
sesman connect ok
sending login info to sesman
login successful for display 14
started connecting
connecting to 127.0.0.1 5914
error - problem connecting
```
xrdp-sesman.log下错误为：

```shell
[INFO ] starting Xvnc session...
[ERROR] X server for display 10 startup timeout[INFO ] starting xrdp-sessvc - xpid=2924 - wmpid=2923
[ERROR] X server for display 10 startup timeout
[ERROR] another Xserver is already active on display 10
[DEBUG] aborting connection...
```
如果你的log也有类似 X server for display 10 startup timeout和another Xserver is already active on display 10
问题根源在于tightvnc，出现问题的版本跟X字体有冲突，导致连接Xserver出错。
解决方法：
```shell
sudo apt-get purge tightvnc xrdp
sudo apt-get install tightvncserver xrdp
```
启动xrdp服务，`sudo /etc/init.d/xrdp restart`，此时会启用相应端口，配置方面默认即可。
可以通过命令：`netstat  -tnl`查看端口

#### 3.无桌面
如果使用该命令指定桌面，`echo "gnome-session --session=gnome-classic" > ~/.xsession`，
xrdp支持不了13.10的gnome了，解决办法是装个xfce界面，指定xfce
```shell
sudo apt-get install xubuntu-desktop

echo xfce4-session >~/.xsession
```

设置配置文件
```shell
sudo gedit /etc/xrdp/startwm.sh
```

在. /etc/X11/Xsession 前一行插入
```shell
xfce4-session
```
重启之后进行连接