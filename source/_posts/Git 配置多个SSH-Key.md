---
title: Git 配置多个SSH-Key
date: '2018/9/30 00:01:45'
updated: '2018/9/30 00:04:11'
tags:
  - Git
---
>windows用户打开Git Bash来执行ssh-keygen命令。

## 配置Git用户名和邮箱
```bash
$ git config --global user.name "用户名"
$ git config --global user.email "youremail@xxx.com"
```

<!-- more -->

## 生成SSH key
生成SSH key，并指定文件名，避免覆盖原有的默认`id_rsa`文件。
生成多个不懂的SSH key只需指定不懂的名字就可以，如下中的`github_rsa`和之前默认生成的`id_rsa`区分开。
```bash
# 一个公司用的
$ ssh-keygen -t rsa -C "youremail@yourcompany.com" -f ~/.ssh/id-rsa

# 一个github用的
$ ssh-keygen -t rsa -C "youremail@your.com" -f ~/.ssh/github-rsa
```

## 添加新的SSH key
默认SSH只会读取id_rsa，所以为了让SSH识别新的私钥，需要将其添加到SSH agen
使用命令：
```bash
$ ssh-add ~/.ssh/id_rsa
$ ssh-add ~/.ssh/github_rsa
```
如果报错：`Could not open a connection to your authentication agent.无法连接到ssh agent`可执行命令：
```bash
$ ssh-agent bash
```
然后在运行`ssh-add`

```bash
# 可以通过 ssh-add -l 来确私钥列表
$ ssh-add -l
# 可以通过 ssh-add -D 来清空私钥列表
$ ssh-add -D
```

## 配置config文件
在`~/.ssh`下添加config文件，如果已经存在，就直接打开修改。
```bash
$ touch ~/.ssh/config   // 创建
```

添加如下内容
```yml
# gitlab
    Host git.iboxpay.com
    HostName git.iboxpay.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
    User anotherUser

# github
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/github_rsa
    User anotherUser
```
每个账号单独配置一个Host，每个Host要取一个别名，每个Host主要配置HostName和IdentityFile两个属性即可。
- Host的名字可以取为自己喜欢的名字，不过这个会影响git相关命令，例如：
Host mygithub 这样定义的话，命令如下，即git@后面紧跟的名字改为mygithub
git clone git@mygithub:PopFisher/AndroidRotateAnim.git
- HostName  这个是真实的域名地址
- IdentityFile 这里是id_rsa的地址，也就是指定私钥的路径
- PreferredAuthentications   配置登录时用什么权限认证--可设为publickey,password publickey,keyboard-interactive等
- User 配置使用用户名

## 测试SSH key
```bash
ssh -T git@git.com  // 或者其他域名地址
```
输出：
Hi XXX! You've successfully authenticated, but GitHub does not provide shell access.