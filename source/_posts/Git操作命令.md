---
title: Git操作命令
date: '2018/7/16 18:41:33'
updated: '2018/9/25 22:27:10'
tags:
  - Git
  - 命令
---
>原文：[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

### 1.设置用户名和邮箱
```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

<!-- more -->

### 2.`pwd`命令用于显示当前目录
```shell
$ mkdir learngit
$ cd learngit
$ pwd
/f/GitStore/learngit
```

### 3.初始化仓库，生成.git文件
```shell
$ git init
Initialized empty Git repository in F:/GitStore/learngit/.git/
```

### 4.将文件添加到暂存区
```shell
$ git add <file>
```

### 5.提交，`-m`后面输入的是本次提交的说明
```shell
$ git commit -m <message>
[master (root-commit) eaadf4e] wrote a readme file
1 file changed, 2 insertions(+)
create mode 100644 readme.txt
```

### 6.查看当前git库状态
```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

### 7.`git diff`顾名思义就是查看difference，修改了哪些内容
```shell
$ git diff <file>
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
Git is free software.
```

### 8.查看日志
```shell
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

### 9.查看日志massage
```shell
$ git log --pretty=oneline
1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
e475afc93c209a690c39c13a46716e8fa000c366 add distributed
eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file

#查看分之合并图
$ git log --graph
$ git log --graph --pretty=oneline --abbrev-commit
```

### 10.会退到上一个版本
Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个^比较容易数不过来，所以写成`HEAD~100`。
```shell
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
或
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```

### 11.查看命令日志
```shell
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

### 12.把文件在工作区的修改全部撤销，其实是用版本库里的版本替换工作区的版本
```shell
$ git checkout -- <file>
```

### 13.把暂存区的修改撤销掉
```shell
git reset HEAD <file>
```

### 14.删除
```shell
$ git rm test.txt
rm 'test.txt'
```

### 15.创建SSH key，在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件
```shell
$ ssh-keygen -t rsa -C "youremail@example.com"
```

### 16.github管理本地仓库，并将本地仓库内容推送到远程库中
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
```shell
$ git remote add origin https://github.com/Bored-AnnoYing/learngit.git
$ git push -u origin master
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 4 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (13/13), 1.04 KiB | 55.00 KiB/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), done.
To https://github.com/Bored-AnnoYing/learngit.git
* [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
### 17.本地提交到远程库
```shell
$ git push origin master
```

### 18.克隆一个本地库
```shell
$ git clone https://github.com/Bored-AnnoYing/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.

```

### 19.创建并切换到分支，`-b`参数表示创建并切换
```shell
$ git checkout -b dev
Switched to a new branch 'dev'
或
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

### 20.查看当前分支，`git branch`命令会列出所有分支，当前分支前面会标一个`*`号
```shell
$ git branch
* dev
  master
```

### 21.合并指定分支到当前分支， 请注意`--no-ff`参数，表示禁用`Fast forward`，因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去
```shell
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
readme.txt | 1 +
1 file changed, 1 insertion(+)
或
$ git merge --no-ff -m "merge with no-ff" dev
```

### 22.删除分支
```shell
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

### 23.将当前工作“储藏”起来，等以后恢复现场后继续工作
```shell
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

### 24.查看“储藏”起来的工作
```shell
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

### 25.恢复并删除stash内容
```shell
$ git stash apply
$ git stash drop
或
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```

### 26.强制删除分支
```shell
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
```

### 27.查看远程仓库信息， `-v`显示更详细
```shell
$ git remote -v
origin  https://github.com/Bored-AnnoYing/learngit.git (fetch)
origin  https://github.com/Bored-AnnoYing/learngit.git (push)

```

### 28.从远程仓库抓取到本地，如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`
```shell
$ git pull
```

### 29.指定本地`dev`分支与远程`origin/dev`分支的链接
```shell
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

### 30.变基，rebase操作可以把本地未push的分叉提交历史整理成直线
```shell
$ git rebase
```

### 31.标签
>**==注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 #ff0000==**
```shell
$ git tag v1.0

#使用commit id打标签
$ git tag v0.9 f52c633

#创建带有说明的标签
$ git tag -a v0.1 -m "version 0.1 released" 1094adb

#查看所有标签
$ git tag
```

### 32.查看标签信息
```shell
$ git show <tagname>
```

### 33.删除标签
```shell
$ git tag -d v0.1
Deleted tag 'v0.1' (was f15b0dd)
```

### 34.推送标签到远程仓库
```shell
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
* [new tag]         v1.0 -> v1.0
```

### 35.推送所有标签到远程仓库
```shell
$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
* [new tag]         v0.9 -> v0.9
```

### 36.删除远程仓库标签，先从本地删除，然后，从远程删除。删除命令也是push
```shell
$ git tag -d v0.9
Deleted tag 'v0.9' (was f52c633)

$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
- [deleted]         v0.9
```
>忽略某些文件时，需要编写.gitignore

### 37.检查`.gitignore`文件哪行做了限制
```shell
$ git check-ignore -v App.class
.gitignore:3:*.class    App.class
```

### 38.配置命令别名，`--global`参数是全局参数，每个仓库的Git配置文件都放在`.git/config`文件中
```shell
$ git config --global alias.st status
```