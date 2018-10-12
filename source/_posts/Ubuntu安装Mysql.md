---
title: Ubuntu安装Mysql
date: '2018/7/31 17:06:40'
updated: '2018/9/25 22:28:08'
tags:
  - Ubuntu
  - Mysql
---
### 1.使用命令进行安装
```shell
sudo apt-get update
sudo apt-get install mysql-server
```

<!-- more -->

### 2.检查
```shell
sudo netstat -tap | grep mysql
```

![mysql install success](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537883882416.png)

### 3.设置mysql远程访问
注释掉bind-address = 127.0.0.1：
```shell
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

![bind-address](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537883882426.png)

### 4.链接mysql进行授权
```shell
mysql -u root -p
```
执行sql进行授权
```sql       
mysql> use mysql;
mysql> select 'host' from user where user='root';
mysql> update user set host = '%' where user = 'root';
mysql> flush privileges;
mysql> select 'host' from user where = 'root';
```

### 5.重启mysql服务
```shell
/etc/init.d/mysql restart
```