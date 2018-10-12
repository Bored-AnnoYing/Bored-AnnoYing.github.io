---
title: Ubuntu安装JDK
date: '2018/7/31 16:50:23'
updated: '2018/9/25 22:28:21'
tags:
  - Ubuntu
  - JDK
---
### 1.源码包准备
从[Oracle官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载`jdk-8u181-linux-x64.tar.gz`

### 2.解压缩
将jdk安装到opt下首先创建java文件夹,然后将源码包放到java文件夹下，执行解压缩

<!-- more -->

```shell
sudo cd /opt/
sudo mkdir java
tar -zxvf jdk-8u181-linux-x64.tar.gz
```
解压缩完成后是ls查看发现会多出`jdk1.8.0_181`的文件夹

![jdk](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537883968403.png)

### 3.配置环境变量
```shell
sudo vi /etc/profile
```
在最后面加上
```shell
export JAVA_HOME=/opt/java/jdk1.8.0_181
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

执行`source profile`时生效

### 4.检查
输入java -version检查是否安装成功
```shell
java -version
```

![java -version](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537883968404.png)