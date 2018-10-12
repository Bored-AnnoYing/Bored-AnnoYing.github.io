---
title: gitlab问题整理
date: '2018/8/20 15:42:23'
updated: '2018/9/25 22:27:31'
tags:
  - gitlab
---
## 一 github服务器切换ip后需修改以下配置
### 1.修改gitlab.rb中的external_url
```shell
cd /etc/gitlab/gitlab.rb
# xxx为修改后的ip
external_url 'http://xxx'
```

<!-- more -->

### 2.修改gitlab.yml中的host
```shell
cd /var/opt/gitlab/gitlab-rails/etc/gitlab.yml
# xxx为修改后的ip
host: xxx
```
### 3.重启gitlab服务
```shell
gitlab-ctl restart
```

## 二 github切换ip后本地改动

### 1.现象

![错误信息](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-8-20/1534748217060.png)

### 2.切换url
```shell
git remote set-url origin git@172.21.7.11:HairRobot/micro-services/haier-robot-maintenance.git
```
或
```shell
git remote rm origin
git remote add origin git@172.21.7.11:HairRobot/micro-services/haier-robot-maintenance.git
```