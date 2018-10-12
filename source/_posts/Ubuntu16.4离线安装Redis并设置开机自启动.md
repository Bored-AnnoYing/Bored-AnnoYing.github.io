---
title: Ubuntu16.4离线安装Redis并设置开机自启动
date: '2018/8/13 20:59:03'
updated: '2018/9/30 11:02:46'
tags:
  - Ubuntu
  - Redis
---
## 安装
### 1.下载所需Redis版本
[Redis github地址：](https://github.com/antirez/redis/releases)https://github.com/antirez/redis/releases

### 2.将文件复制到 `/opt/redis/` 文件夹下

### 3.解压
```shell
sudo tar -zxvf redis-4.0.10.tar.gz
```

<!-- more -->

### 4.然后进入redis-4.0.10文件下，执行
```shell
sudo make
sudo make install
```

## 配置

### 1.将配置文件复制到上层目录
```shell
sudo cp redis.conf ../redis.conf
```

### 2.修改配置文件
```shell
cd ..
sudo vi redis.conf
```

```shell
# 日志文件输出
logfile "/opt/redis/logs/redis.log"
# 配置密码
requirepass Www#1234
# 设置为后台运行程序
daemonize yes
# 注释掉下面这句否则连不上
# bind 127.0.0.1
```

### 3.启动
```shell
cd /usr/local/bin
# 使用root用户执行
redis-server /opt/redis/redis.conf
```

### 4.检查
```shell
ps -ef|grep redis
```

![检查](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-8-13/1534164796274.png)

```shell
redis-cli -p 6379
127.0.0.1:6379> ping
```

![检查](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-8-13/1534164949985.png)

## 停止
```shell
redis-cli -h 127.0.0.1 -p 6379 shutdown
```
如果上述方式都没有成功停止redis，则可以使用终极武器 `kill -9`


## 开机自启动

### 1、配置服务脚本
在Redis安装路径之下，找到`utils`目录，其中有个`redis_init_script`文件。将其作为自启动配置文件。将其复制到`/etc/init.d/`下。
作为系统启动服务名（以d结尾表示是自启动服务，约定俗成）。
```shell
sudo cp /opt/redis/redis-4.0.10/utils/redis_init_script /etc/init.d/redisd
```

打开文件修改文件的启动目录
```shell
sudo vim /etc/init.d/redisd
```

```shell
# 端口
REDISPORT=6379
# redis-server的路径
EXEC=/opt/redis/redis-4.0.10/src/redis-server
# redis-cli的路径
CLIEXEC=/opt/redis/redis-4.0.10/src/redis-cli
# redis.conf里有这个参数，将其复制过来
PIDFILE=/var/run/redis_6379.pid
# redis.conf的路径
CONF="/opt/redis/redis.conf"
```

```bash
case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
            echo "$PIDFILE exists, process is already running or crashed"
        else
            echo "Starting Redis server..."
            $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
            echo "$PIDFILE does not exist, process is not running"
        else
            PID=$(cat $PIDFILE)
            echo "Stopping ..."
            $CLIEXEC -p $REDISPORT shutdown
            while [ -x /proc/${PID} ]
            do
                echo "Waiting for Redis to shutdown ..."
                sleep 1
            done
            echo "Redis stopped"
        fi
        ;;
    status)
        PID=$(cat $PIDFILE)
        if [ ! -x /proc/${PID} ]
        then
            echo 'Redis is not running'
        else
            echo "Redis is running ($PID)"
        fi
        ;;
    restart)
        $0 stop
        $0 start
        ;;
    *)
        echo "Please use start, stop, restart or status as first argument"
        ;;
esac
```
### 2、使用update-rc.d注册服务

```shell
#设置服务脚本有执行权限
sudo chmod +x /etc/init.d/redisd
#注册服务
cd /etc/init.d/
sudo update-rc.d redisd defaults
```

### 3、服务常用命令
```shell
#启动Redis服务
sudo service redisd start  
#关闭服务
sudo service redisd stop  
#重启服务：
sudo service redisd restart
#
sudo service redisd status
#在控制台中进入redis客户端
sudo redis-cli
```

### 4、可能会遇到的问题
>参考：[Ubuntu配置Redis并设置为开机启动](https://www.jianshu.com/p/3ffb2b5467f4)

在注册服务时：

![](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-30/1538275206407.png)

可以将头部的`INIT INFO`信息替换成下面的：
```shell
###############
# SysV Init Information
# chkconfig: - 58 74
# description: redis is the redis daemon.
### BEGIN INIT INFO
# Provides: redis
# Required-Start: $network $local_fs $remote_fs
# Required-Stop: $network $local_fs $remote_fs
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Should-Start: $syslog $named
# Should-Stop: $syslog $named
# Short-Description: start and stop redis
# Description: Redis daemon
### END INIT INFO
```