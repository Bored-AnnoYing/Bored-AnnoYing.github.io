---
title: Ubuntu16.04搭建gitlab
date: '2018/8/01 15:33:44'
updated: '2018/9/25 22:27:42'
tags:
  - Ubuntu
  - gitlab
---
## 安装
[Ubuntu 16.04 x64搭建GitLab服务器操作笔记](https://blog.csdn.net/discoverer100/article/details/51814171)
[Ubuntu16.04 安装Gitlab](https://blog.csdn.net/goldDaNiu/article/details/81116179)

gitla 仓库默认存储路径：`/var/opt/gitlab/git-data/repositories`
### 1.安装依赖包
```shell
sudo apt-get install curl openssh-server ca-certificates postfix
```

<!-- more -->

执行完成后，出现邮件配置，选择Internet那一项（不带Smarthost的）

![Internet Site](./images/1533108396711.png)

### 2.利用清华大学的镜像`https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/`来进行主程序的安装。
首先信任 GitLab 的 GPG 公钥:
```shell
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```
### 3.利用root用户1（不是sudo，而是root），vi打开文件/etc/apt/sources.list.d/gitlab-ce.list，加入下面一行：
```shell
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main
```

### 4.安装 gitlab-ce:
```shell
sudo apt-get update
sudo apt-get install gitlab-ce
```

### 5.执行命令
```shell
sudo gitlab-ctl reconfigure
```

### 6.打开 sshd 和 postfix 服务
```shell
service sshd start
service postfix start
```

### 7.了使 GitLab 社区版的 Web 界面可以通过网络进行访问，我们需要允许 80 端口通过防火墙，这个端口是 GitLab 社区版的默认端口。为此需要运行下面的命令
```shell
sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
```

### 8.检查GitLab是否安装好并且已经正确运行，输入下面的命令
```shell
sudo gitlab-ctl status
```

### 9.如果得到类似下面的结果，则说明GitLab运行正常
```shell
run: gitlab-workhorse: (pid 1148) 884s; run: log: (pid 1132) 884s
run: logrotate: (pid 1150) 884s; run: log: (pid 1131) 884s
run: nginx: (pid 1144) 884s; run: log: (pid 1129) 884s
run: postgresql: (pid 1147) 884s; run: log: (pid 1130) 884s
run: redis: (pid 1146) 884s; run: log: (pid 1133) 884s
run: sidekiq: (pid 1145) 884s; run: log: (pid 1128) 884s
run: unicorn: (pid 1149) 885s; run: log: (pid 1134) 885s
```
### 10.在浏览器地址栏中输入：http://127.0.0.1，即可访问GitLab的Web页面

![设置root账号密码](./images/1533108800458.png)

## 汉化
### 1.在仓库中创建`gitlab_cn`文件夹
```shell
cd /var/opt/gitlab/git-data/repositories/
mkdir gitlab_cn
cd gitlab_cn
```
### 2.克隆gitlab仓库到本地。 xhang项目 比较新，所以在这拉取xhang项目
[larryli项目：](https://gitlab.com/larryli/gitlab)https://gitlab.com/larryli/gitlab
[xhang项目：](https://gitlab.com/xhang/gitlab)https://gitlab.com/xhang/gitlab
```shell
# 克隆 GitLab.com 仓库
git clone https://gitlab.com/larryli/gitlab.git
＃或 Gitcafe.com 镜像，速度更快
git clone https://gitcafe.com/larryli/gitlab.git

# xhang项目
git clone https://gitlab.com/xhang/gitlab.git
```
如果已有此项目，则更新：
```shell
git fetch
```

### 3.确定Gitlab安装版本
```shell
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
```

![Gitlab版本](./images/1533181922727.png)

### 4.生成汉化补丁
```shell
# 进入克隆仓库得到的gitlab目录
cd gitlab
# 查看tag版本，选择合适的汉化版本
git tag

# 对比不同，这里比较的是tag，v11.1.2为英文原版，v11.1.2-zh为汉化版本。diff结果是汉化补丁。
git diff v11.1.2..v11.1.2-zh > /tmp/11.1.2.diff
```
### 5.打完补丁后启动就可以了
```shell
# 停止gitlab
gitlab-ctl stop
# 应用汉化补丁
cd /opt/gitlab/embedded/service
# 记得备份
cp -r gitlab-rails/ gitlab-rails-bak/
cd gitlab-rails
git apply /tmp/9.2.6.diff
# 启动gitlab
gitlab-ctl start
```
### 可能遇到的问题

![enter description here](./images/1533185214840.png)

解决方法：[https://gitlab.com/xhang/gitlab/uploads/23e8414e53eefde0968e960b24a6f631/_%E5%8E%9F%E5%88%9B_Gitlab10.8.0%E5%88%86%E6%94%AF%E6%B1%89%E5%8C%96_Omnibus%E7%89%88_.png](https://gitlab.com/xhang/gitlab/uploads/23e8414e53eefde0968e960b24a6f631/_%E5%8E%9F%E5%88%9B_Gitlab10.8.0%E5%88%86%E6%94%AF%E6%B1%89%E5%8C%96_Omnibus%E7%89%88_.png)
[10.8.1版本补丁错误 ：](https://gitlab.com/xhang/gitlab/issues/63)https://gitlab.com/xhang/gitlab/issues/63


#### 1.首先删除之前生成的补丁
```shell
rm /tmp/11.1.2.diff
```
切换到gitlab目录下
```shell
cd /var/opt/gitlab/git-data/repositories/gitlab_cn/gitlab/
```

### 2.生成补丁
app/assets 已经不在包含于部署的代码中，因此 git diff 的时候需要跳过这个目录
```shell
IGNORE_DIRS=':!spec :!features :!.gitignore :!locale :!app/assets/ :!vendor/assets/'
git diff --diff-filter=d v11.1.2..v11.1.2-zh -- . ${IGNORE_DIRS} > /tmp/11.1.2-zh_CN.diff
```
### 3.打补丁
```shell
cd /opt/gitlab/embedded/service/gitlab-rails
patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < /tmp/11.1.2-zh_CN.diff
```
![打补丁](./images/1533185948771.png)

### 4.将缺少的目录和文件复制到服务器

![缺少的文件](./images/1533188575206.png)
将汉化仓库中`app/assets`文件夹和`vendor/assets`复制到服务器
```shell
cd /opt/gitlab/embedded/service/gitlab-rails/app/
cp -r /var/opt/gitlab/git-data/repositories/gitlab_cn/gitlab/app/assets/ assets

cd /opt/gitlab/embedded/service/gitlab-rails/vendor/
cp -r /var/opt/gitlab/git-data/repositories/gitlab_cn/gitlab/vendor/assets/ assets
```

### 5.然后启动就可以愉快的玩耍了
```shell
gitlab-ctl start
```

![启动](./images/1533185878559.png)

## 其他事项

### 1.修改gitlab配置文件指定服务器ip和自定义端口：
```shell
sudo vi /etc/gitlab/gitlab.rb
```

![gitlab.rb](./images/1533108903666.png)

每次修改/etc/gitlab/gitlab.rb，都要运行以下命令，让配置生效
设置完成后重新加载启动：
```shell
gitlab-ctl reconfigure
gitlab-ctl restart
```

### 2.备份
参考文档：
[快速安装 GitLab 并汉化](https://www.jianshu.com/p/7a0d6917e009?mType=Group)
[git学习------> Gitlab如何进行备份恢复与迁移？](https://blog.csdn.net/ouyang_peng/article/details/77070977)
#### 1.备份配置文件
配置文件含密码等敏感信息，不要和数据备份文件放在一起。
```shell
# sh -c 'umask 0077; tar -cf $(date "+etc-gitlab-%s.tar") -C /etc/gitlab'
```

#### 2.备份gitlab
默认数据备份目录是`/var/opt/gitlab/backups`，手动创建备份文件
```shell
# Omnibus 方式安装使用以下命令备份
sudo gitlab-rake gitlab:backup:create
```

#### 3.定时备份gitlab
方法1.在命令行输入: crontab -e 然后添加相应的任务，wq存盘退出。
```shell
#输入命令crontab -e
sudo crontab -e  
#输入相应的任务
0 2 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create CRON=1  
```
方法2、直接编辑/etc/crontab 文件，即vi /etc/crontab，然后添加相应的任务
```shell
#编辑 /etc/crontab
vi /etc/crontab
# edited by ouyang 2017-8-11 添加定时任务，每天凌晨两点，执行gitlab备份
0  2    * * *   root    /opt/gitlab/bin/gitlab-rake gitlab:backup:create CRON=1  
```

编写完 /etc/crontab 文件之后，需要重新启动cron服务
```shell
#重新加载cron配置文件
sudo /usr/sbin/service cron reload
#重启cron服务
sudo /usr/sbin/service cron restart
```

#### 4.设置配置文件
修改`/etc/gitlab/gitlab.rb`文件
```shell
# 备份保留天数为3个月（即90天，这里是7776000秒）
gitlab_rails['backup_keep_time'] = 7776000
# 默认备份权限
# gitlab_rails['backup_archive_permissions'] = 0644
# 默认本分路径
# gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"
```
修改完成之后使用下面命令重载配置文件
```shell
gitlab-ctl reconfigure
```

### 3、恢复
>**==确保备份文件所安装 GitLab 和当前要恢复的 GitLab 版本一致 #ff0000==**

#### 1.复制数据文件，将备份文件权限修改为777，不然可能恢复的时候会出现权限不够，不能解压的问题
```shell
# 将数据备份文件拷贝至备份目录
chmod 777 1502357536_2017_08_10_9.4.3_gitlab_backup.tar
sudo cp 1393513186_gitlab_backup.tar /var/opt/gitlab/backups/
```

#### 2.执行命令停止相关数据连接服务
```shell
# 停止相关数据连接服务
gitlab-ctl stop unicorn
gitlab-ctl stop sidekiq
```

#### 3.执行命令从备份文件中恢复Gitlab
`*_gitlab_backup.tar`*表示备份文件编号
例如：`1502357536_2017_08_10_9.4.3_gitlab_backup.tar` 文件编号为`1502357536_2017_08_10_9.4.3`

```shell
gitlab-rake gitlab:backup:restore BACKUP=备份文件编号
```

#### 4.启动Gitlab
```shell
sudo gitlab-ctl start
```