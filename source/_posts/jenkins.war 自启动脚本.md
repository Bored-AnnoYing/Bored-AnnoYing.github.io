---
title: jenkins.war 自启动脚本
date: '2018/5/24 17:42:24'
updated: '2018/9/25 22:29:48'
tags:
  - jenkins
  - linux
  - 自启动
---
> [centos/ubuntu jenkins.war 自启动脚本,Linux启动停止jar包的运行案例](https://blog.csdn.net/fenglailea/article/details/42557751)
> [jenkins 作为 linux的启动项](http://alanland.iteye.com/blog/2047249)

<!-- more -->

```shell
#! /bin/sh
# chkconfig: 2345 10 90
# description: jenkins ....
# This script will be executed *after* all the other init scripts.  
# You can put your own initialization stuff in here if you don't  
# want to do the full Sys V style init stuff.  
#prefix=/home/lanmps/jenkins
#nohup $prefix/start_jenkins.sh >> $prefix/jenkins.log 2>&1 &
#风来了.呆狐狸

JENKINS_ROOT=/home/lanmps/jenkins
JENKINSFILENAME=jenkins.war

#停止方法
stop(){
    echo "Stoping $JENKINSFILENAME "
    ps -ef|grep $JENKINSFILENAME |awk '{print $2}'|while read pid
    do
       kill -9 $pid
       echo " $pid kill"
    done
}

case "$1" in
start)
    echo "Starting $JENKINSFILENAME "
    nohup $JENKINS_ROOT/start_jenkins.sh >> $JENKINS_ROOT/jenkins.log 2>&1 &
  ;;
stop)
  stop
  ;;
restart)
  stop
  start
  ;;
status)
  ps -ef|grep $JENKINSFILENAME
  ;;
*)
  printf 'Usage: %s {start|stop|restart|status}\n' "$prog"
  exit 1
  ;;
esac
```

保存为jenkins目录下 jenkins.sh
**JENKINS_ROOT为 jenkins目录**

```shell
#!/bin/bash  
JENKINS_ROOT=/home/lanmps/jenkins  
export JENKINS_HOME=$JENKINS_ROOT/home  
java -jar $JENKINS_ROOT/jenkins.war --httpPort=6700
```

保存为：start_jenkins.sh

### 1.centos

```shell
ln -s /home/lanmps/jenkins/jenkins.sh /etc/init.d/jenkins  
chkconfig --add jenkins  
chkconfig --level 345 jenkins on  
```
启动方式
```shell
/etc/init.d/jenkins start  
```

### 2.ubuntu
```shell
sudo ln -s /home/lanmps/jenkins/jenkins.sh /etc/init.d/jenkins
sudo chmod a+x /etc/init.d/jenkins
update-rc.d -f jenkins defaults  
```
启动方式
```shell
/etc/init.d/jenkins start  
```