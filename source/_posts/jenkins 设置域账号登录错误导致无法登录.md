---
title: jenkins 设置域账号登录错误导致无法登录
date: '2018/5/24 17:56:39'
updated: '2018/9/25 22:29:33'
tags:
  - jenkins
  - linux
---
>原文： [Jenkins -- 设置域账号登录错误导致无法登录](https://blog.csdn.net/wudj810818/article/details/51426642)

设置LDAP认证登录后，发现设置错误导致无法登陆Jenkins。为了去除认证登录的设置，可以采取如下操作：

1.停止jenkins
2.修改$JENKINS_HOME下的config.xml (一般为 /root/.jenkins下)

<!-- more -->

- 将<useSecurity>true</useSecurity>元素中的true改为false
- 将<authorizationStrategy>和<securityRealm>元素的内容删掉

3.启动Jenkins

![](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884480671.png)


### 进入jenkins之后

1.首先进入全局安全配置

![第一步](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884579577.png)

2.设置如下，并保存后退出

![第二步](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884528767.png)

3.进入管理用户，将之前配置错误的用户干掉

![第三步](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884613371.png)

4.重新新建用户

![第四步](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884643873.png)

5.回到之前的全局安全配置中，授权策略选择==项目矩阵授权策略==，在添加用户组中输入刚才新建的用户“admin”-->添加，为admin用户授权完成后，保存即可

![第五步](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884655152.png)