---
title: Ubuntu16.04安装RabbitMQ
date: '2018/8/14 12:01:24'
updated: '2018/9/25 22:27:49'
tags:
  - Ubuntu
  - RabbitMQ
---
### 1.添加源
```shell
echo "deb https://dl.bintray.com/rabbitmq/debian xenial main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list
```

<!-- more -->

### 2.新增公钥
```shell
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
```

### 3.更新源
```shell
sudo apt-get update
```

### 4.安装rabbitmq-server
```shell
sudo apt-get install rabbitmq-server
```

### 5.查看安装的插件
```shell
sudo rabbitmq-plugins enable rabbitmq_management
```

![安装的插件](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-8-14/1534219085921.png)

### 6.查看是否安装成功
```shell
netstat -tunlp | grep beam
```

### 7.查看用户
```shell
sudo rabbitmqctl list_users
```

### 8.添加用户
```shell
sudo rabbitmqctl add_user admin admin
sudo rabbitmqctl set_user_tags admin administrator
```

### 9.远程访问
```shell
rabbitmqctl set_permissions -p "/" hollysys ".*" ".*" ".*"
```

### 10.重启RabbitMQ
```shell
# 停止rabbitmq
rabbitmqctl stop
# 重启rabbitmq
rabbitmq-server restart
```