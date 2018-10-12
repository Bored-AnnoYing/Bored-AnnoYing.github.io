---
title: docker入门
date: '2018/5/25 18:27:44'
updated: '2018/9/25 22:29:20'
tags:
  - docker
---
>[Docker 微服务教程 - 阮一峰](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)
>[Docker 入门教程 - 阮一峰](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## 一、什么是docker？
---
Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从Apache2.0协议开源。
Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

<!-- more -->

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

>[如何通俗解释Docker是什么？ - 刘允鹏的回答 - 知乎](https://www.zhihu.com/question/28300645/answer/67707287)

Docker的思想来自于集装箱，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。

docker就是类似的理念。现在都流行云计算了，云计算就好比大货轮。docker就是集装箱。

1.不同的应用程序可能会有不同的应用环境，比如.net开发的网站和php开发的网站依赖的软件就不一样，如果把他们依赖的软件都安装在一个服务器上就要调试很久，而且很麻烦，还会造成一些冲突。比如IIS和Apache访问端口冲突。这个时候你就要隔离.net开发的网站和php开发的网站。常规来讲，我们可以在服务器上创建不同的虚拟机在不同的虚拟机上放置不同的应用，但是虚拟机开销比较高。docker可以实现虚拟机隔离应用环境的功能，并且开销比虚拟机小，小就意味着省钱了。
2.你开发软件的时候用的是Ubuntu，但是运维管理的都是centos，运维在把你的软件从开发环境转移到生产环境的时候就会遇到一些Ubuntu转centos的问题，比如：有个特殊版本的数据库，只有Ubuntu支持，centos不支持，在转移的过程当中运维就得想办法解决这样的问题。这时候要是有docker你就可以把开发环境直接封装转移给运维，运维直接部署你给他的docker就可以了。而且部署速度快。
3.在服务器负载方面，如果你单独开一个虚拟机，那么虚拟机会占用空闲内存的，docker部署的话，这些内存就会利用起来。

总之docker就是集装箱原理。

## 二、环境配置的难题（背景）
---

软件开发最大的麻烦事之一，就是环境配置。用户计算机的环境都不相同，你怎么知道自家的软件，能在那些机器跑起来？

用户必须保证两件事：操作系统的设置，各种库和组件的安装。只有它们都正确，软件才能运行。举例来说，安装一个 Python 应用，计算机必须有 Python 引擎，还必须有各种依赖，可能还要配置环境变量。

如果某些老旧的模块与当前环境不兼容，那就麻烦了。开发者常常会说："它在我的机器可以跑了"（It works on my machine），言下之意就是，其他机器很可能跑不了。

环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。

## 三、免sudo使用docker命令

默认安装完 docker 后，每次执行 docker 都需要运行 `sudo` 命令，非常浪费时间影响效率。如果不跟 `sudo`，直接执行 `docker images `命令会有如下问题:
```shell
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.37/images/json: dial unix /var/run/docker.sock: connect: permission denied
```

首先添加docker用户组
```shell
sudo groupadd docker
```

然后将当前用户添加到docker用户组中
```shell
sudo gpasswd -a ${USER} docker
```

重启 docker 服务
```shell
sudo service docker restart
```

切换会话，因为 groups 命令获取到的是缓存的组信息，刚添加的组信息未能生效，所以 docker images 执行时同样有错
```shell
newgrp - docker
```

## 四、docker修改image 仓库的镜像网址

Docker 的官方仓库很慢，还经常断线，所以要把仓库网址改成国内的镜像站。
打开`/etc/default/docker`文件（需要`sudo`权限），在文件的底部加上一行。
```shell
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"
```
然后，重启 Docker 服务。

```shell
sudo service docker restart
```

## 五、image文件
---

**Docker 把应用程序及其依赖，打包在 image 文件里面**。只有通过这个文件，才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

image 是二进制文件。实际开发中，一个 image 文件往往通过继承另一个 image 文件，加上一些个性化设置而生成。举例来说，你可以在 Ubuntu 的 image 基础上，往里面加入 Apache 服务器，形成你的 image。
```shell
# 查看image文件
docker images

# 删除image文件
docker image rm [imageName]
```

## 六、实例：hello world
---
下面，我们通过最简单的 image 文件[“hello world”](https://hub.docker.com/r/library/hello-world/)，感受一下 Docker。

需要说明的是，国内连接 Docker 的官方仓库很慢，还会断线，需要将默认仓库改成国内的镜像网站，具体的修改方法在下一篇文章的第一节。有需要的朋友，可以先看一下。

首先，运行下面的命令，将 image 文件从仓库抓取到本地。
```shell
docker image pull library/hello-world
```
上面代码中，`docker image pull`是抓取 image 文件的命令。`library/hello-world`是 image 文件在仓库里面的位置，其中`library`是 image 文件所在的组，`hello-world`是 image 文件的名字。

由于 Docker 官方提供的 image 文件，都放在`library`组里面，所以它的是默认组，可以省略。因此，上面的命令可以写成下面这样。
```shell
docker image pull hello-world
```
抓取成功以后，就可以在本机看到这个 image 文件了。
```shell
docker image ls
```
现在，运行这个 image 文件。
```shell
docker container run hello-world
```
`docker container run`命令会从 image 文件，生成一个正在运行的容器实例。

注意，`docker container run`命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。因此，前面的`docker image pull`命令并不是必需的步骤。

如果运行成功，你会在屏幕上读到下面的输出。
```shell
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
https://hub.docker.com/

For more examples and ideas, visit:
https://docs.docker.com/engine/userguide/
```
输出这段提示以后，`hello world`就会停止运行，容器自动终止。

有些容器不会自动终止，因为提供的是服务。比如，安装运行 Ubuntu 的 image，就可以在命令行体验 Ubuntu 系统。
```shell
docker container run -it ubuntu bash
```
对于那些不会自动终止的容器，必须使用`docker container kill` 命令手动终止，`containID`输入前三位即可。
```shell
docker container kill [containID]
```

## 七、容器文件
---
image 文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已。
```shell
# 列出本机正在运行的容器
docker container ls
# 或者
docker ps

# 列出本机所有容器，包括终止运行的容器
docker container ls --all
# 或者
docker ps -a
```

上面命令的输出结果之中，包括容器的 ID。很多地方都需要提供这个 ID，比如上一节终止容器运行的`docker container kill`命令。

终止运行的容器文件，依然会占据硬盘空间，可以使用`docker container rm`命令删除。
```shell
docker container rm [containerID]
```
运行上面的命令之后，再使用`docker container ls --all`命令，就会发现被删除的容器文件已经消失了。

## 八、Dockerfile 文件
学会使用 image 文件以后，接下来的问题就是，如何可以生成 image 文件？如果你要推广自己的软件，势必要自己制作 image 文件。

这就需要用到 Dockerfile 文件。它是一个文本文件，用来配置 image。Docker 根据 该文件生成二进制的 image 文件。

下面通过一个实例，演示如何编写 Dockerfile 文件。

## 九、实例：制作自己的Docker容器

### 10.1 编写 Dockerfile 文件
首先，在项目的根目录下，新建一个文本文件`.dockerignore`，写入下面的内容。
```shell
.git
node_modules
npm-debug.log
```
上面代码表示，这三个路径要排除，不要打包进入 image 文件。如果你没有路径要排除，这个文件可以不新建。

然后，在项目的根目录下，新建一个文本文件 Dockerfile，写入下面的内容。
```dockerfile
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
```
上面代码一共五行，含义如下。
```dockerfile?linenums
FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
WORKDIR /app：指定接下来的工作路径为/app。
RUN npm install：在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口。
```
### 10.2 创建 image 文件

有了 Dockerfile 文件以后，就可以使用`docker image build`命令创建 image 文件了。
```shell
docker image build -t koa-demo .
# 或者
docker image build -t koa-demo:0.0.1 .
```
上面代码中，`-t`参数用来指定 image 文件的名字，后面还可以用冒号指定标签。如果不指定，默认的标签就是`latest`。最后的那个点表示 Dockerfile 文件所在的路径，上例是当前路径，所以是一个点。

如果运行成功，就可以看到新生成的 image 文件`koa-demo`了。
```shell
docker images
```
### 10.3 生成容器
`docker container run`命令会从 image 文件生成容器。
```shell
docker container run -p 8000:3000 -it koa-demo /bin/bash
# 或者
docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash
```
上面命令的各个参数含义如下：
```shell?linenums
-p参数：容器的 3000 端口映射到本机的 8000 端口。
-it参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
koa-demo:0.0.1：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
/bin/bash：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。
```
如果一切正常，运行上面的命令以后，就会返回一个命令行提示符。
```shell
root@66d80f4aaf1e:/app#
```
这表示你已经在容器里面了，返回的提示符就是容器内部的 Shell 提示符。执行下面的命令。
```shell
root@66d80f4aaf1e:/app# node demos/01.js
```
这时，Koa 框架已经运行起来了。打开本机的浏览器，访问 `http://127.0.0.1:8000` ，网页显示"Not Found"，这是因为这个 demo 没有写路由。

这个例子中，Node 进程运行在 Docker 容器的虚拟环境里面，进程接触到的文件系统和网络接口都是虚拟的，与本机的文件系统和网络接口是隔离的，因此需要定义容器与物理机的端口映射（map）。

现在，在容器的命令行，按下 Ctrl + c 停止 Node 进程，然后按下 Ctrl + d （或者输入 exit）退出容器。此外，也可以用`docker container kill`终止容器运行。
```shell
# 在本机的另一个终端窗口，查出容器的 ID
docker container ls

# 停止指定的容器运行
docker container kill [containerID]
```
容器停止运行之后，并不会消失，用下面的命令删除容器文件。
```shell
# 查出容器的 ID
docker container ls --all

# 删除指定的容器文件
docker container rm [containerID]
```

也可以使用docker container run命令的--rm参数，在容器终止运行后自动删除容器文件。
```shell
docker container run --rm -p 8000:3000 -it koa-demo /bin/bash
```
### 10.4 CMD 命令
上一节的例子里面，容器启动以后，需要手动输入命令node demos/01.js。我们可以把这个命令写在 Dockerfile 里面，这样容器启动以后，这个命令就已经执行了，不用再手动输入了。
```dockerfile
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
CMD node demos/01.js
```

上面的 Dockerfile 里面，多了最后一行`CMD node demos/01.js`，它表示容器启动后自动执行`node demos/01.js`。

你可能会问，`RUN`命令与`CMD`命令的区别在哪里？简单说，`RUN`命令在 image 文件的构建阶段执行，执行结果都会打包进入 image 文件；`CMD`命令则是在容器启动后执行。另外，一个 Dockerfile 可以包含多个`RUN`命令，但是只能有一个`CMD`命令。

注意，指定了`CMD`命令以后，`docker container run`命令就不能附加命令了（比如前面的`/bin/bash`），否则它会覆盖`CMD`命令。现在，启动容器可以使用下面的命令。
```shell
docker container run --rm -p 8000:3000 -it koa-demo:0.0.1
```

### 10.5 发布 image 文件
容器运行成功后，就确认了 image 文件的有效性。这时，我们就可以考虑把 image 文件分享到网上，让其他人使用。

首先，去 [hub.docker.com](https://hub.docker.com/) 或 [cloud.docker.com](https://cloud.docker.com/) 注册一个账户。然后，用下面的命令登录。
```shell
docker login
```
接着，为本地的 image 标注用户名和版本。
```shell
docker image tag [imageName] [username]/[repository]:[tag]
# 实例
docker image tag koa-demos:0.0.1 ruanyf/koa-demos:0.0.1
```
也可以不标注用户名，重新构建一下 image 文件。
```shell
docker image build -t [username]/[repository]:[tag] .
```
最后，发布 image 文件。
```shell
docker image push [username]/[repository]:[tag]
```
发布成功以后，登录 hub.docker.com，就可以看到已经发布的 image 文件。

## 十、其他有用的命令
docker 的主要用法就是上面这些，此外还有几个命令，也非常有用。

### （1）docker container start
前面的`docker container run`命令是新建容器，每运行一次，就会新建一个容器。同样的命令运行两次，就会生成两个一模一样的容器文件。如果希望重复使用容器，就要使用`docker container start`命令，它用来启动已经生成、已经停止运行的容器文件。
```shell
docker container start [containerID]
```

### （2）docker container stop
前面的`docker container kill`命令终止容器运行，相当于向容器里面的主进程发出 SIGKILL 信号。而`docker container stop`命令也是用来终止容器运行，相当于向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号。
```shell
bash container stop [containerID]
```
这两个信号的差别是，应用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失。

### （3）docker container logs
`docker container logs`命令用来查看 docker 容器的输出，即容器里面 Shell 的标准输出。如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令查看输出。
```shell
docker container logs [containerID]
```

### （4）docker container exec
`docker container exec`命令用于进入一个正在运行的 docker 容器。如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令进入容器。一旦进入了容器，就可以在容器的 Shell 执行命令了。
```shell
docker container exec -it [containerID] /bin/bash
```

### （5）docker container cp
`docker container cp`命令用于从正在运行的 Docker 容器里面，将文件拷贝到本机。下面是拷贝到当前目录的写法。
```shell
docker container cp [containID]:[/path/to/file] .
```