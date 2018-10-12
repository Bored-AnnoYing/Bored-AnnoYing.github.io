---
title: docker和docker-compose安装
date: '2018/5/25 18:26:49'
updated: '2018/9/25 22:29:26'
tags:
  - docker
  - Ubuntu
---
>[ubuntu16.04安装最新版docker、docker-compose、docker-machine - 天涯小嘿嘿](https://www.cnblogs.com/tianhei/p/7802064.html)

### 一、docker安装
前提：Docker需要==64位==的操作系统，kernel内核至少要在3.10版本之上。最近的3.10小版本或者最新的维护版本也是可以接受的。kernel3.10版本之前的系统缺少一些特性来运行docker容器。这些旧版本有些已知的bugs会导致数据丢失并且在一定条件下会频繁的故障。检查你当前的kernel版本，打开终端，输入  uname  -r

<!-- more -->

1.卸载旧版本docker，全新安装时，无需执行该步骤
```shell
sudo apt-get remove docker docker-engine docker.io
```
2.更新源
```shell
sudo apt-get update
```
3.安装以下包以使apt可以通过HTTPS使用存储库（repository）
```shell
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```
4.添加docker的官方GPG密钥执行
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
5.然后可以校验下秘钥，执行    `sudo apt-key fingerprint 0EBFCD88`

看输出的是不是9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

6.添加仓库，一般64位的都选amd,和inter、amd cpu无关，执行
```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
7.更新源
```shell
sudo apt-get update
```
8.安装最新版本的Docker CE
如果想指定安装某一版本，可使用 `sudo apt-get install docker-ce=<VERSION>`  命令，把`<VERSION>`替换为具体版本即可。

以下命令没有指定版本，默认就会安装最新版

```shell
sudo apt-get install docker-ce
```
9.验证docker
```shell
sudo docker version
```

### 二、docker-compose安装

可运行和管理多个docker容器
1.下载docker-compose
```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
2.授权
```shell
sudo chmod +x /usr/local/bin/docker-compose
```
3.查看版本信息
```shell
docker-compose --version
```
显示出版本信息，即安装成功。