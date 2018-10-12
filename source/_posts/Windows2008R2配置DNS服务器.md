---
title: Windows2008R2配置DNS服务器
date: '2017/9/20 13:17:43'
updated: '2018/9/25 22:30:12'
tags:
  - DNS
  - Windows2008R2
---
       由于在本地服务器端使用https协议时，客户端的程序需要对https证书进行验证，所以需要配置一个局域网（host文件只能本机使用）内可以访问的域名（前提是已经有了一个公网CA证书，内网配置公网证书，将内网域名和公网域名设置为相同内容可以跳过访问）
       
1、我的电脑右键->属性->角色->添加角色

![1][1]

<!-- more -->

2、选择dns 服务器，这里我已经安装了。安装过程中没有什么知识要点和注意的，一步步点下去，然后下一步，等待安装完成。

![2][2]

3、安装完成-开始--管理工具--dns，打开。正向查找区域--右键--新建区域，下一步

![3][3]

4、注意！注意！注意！区域名称，就是我们普通说的域名；例如 www.baidu.com 这里区域名称应该填baidu.com，而www，是域名头后面再填写；下一步

![4][4]

5、到我们新建的正向域pf.com里面空白处，右键--新建主机（A或AAA）

![5][5]

6、注意！注意!注意! 设置了，最关键一步；主机，我们填www（你想怎么填都可以，没有具体要求，只是大家习惯了www）；ip地址，就填你服务器本地ip地址（一定要为静态的，知识点来了，既然做了dns服务器那么其他主机要通过你来解析域名，所以这里的ip一定不要填错）；  仔细做好每一步，理解每一步是什么意思，确定。

>==注意：这里IP配置里面，DNS和IP一定要一样 #f91906==

![6][6]

![7][7]

7、正向查找上面做完了，我们开始做反向查找区域--右键--新建区域，下一步

![8][8]

8、选择IP4，反向查询，填好反向查找区域IP段，下一步

![9][9]

9、进入反向查找区域--刚才ip段里面--空白处右键--新建指针。

![10][10]

10、主机IP地址，就是dns服务器ip；主机名，游览--选择正向区域--pf.com里面的www文件--确定，再确定

![11][11]

![12][12]

11、做到这里，正向反向我们都做完了，怎么检查是否有效；dns管理器--启动 nslookup--依次输入我们实验域名www.pf.com 和ip地址，看下面信息是否匹配；大功告成， 域名www.pf.com 为实验域名，和真实域名毫不关系

![13][13]

![14][14]

>注意：
>1，    正向域名和反向域名，一个是域名绑定IP，一个是IP绑定域名
>2，    手机连接或者电脑连接访问的时候只需要在同一个局域网下，并且需要将DNS设置和服务器的相同
>3，    如果路由器有防火墙需要将DNS服务器转发出去
>4，    如果有实体防火墙需要将53端口开放，53端口就是DNS服务器默认端口



  [1]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884825636.png
  [2]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884966221.png
  [3]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537884991766.png
  [4]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885005197.png
  [5]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885018978.png
  [6]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885035223.png
  [7]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885050544.png
  [8]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885065855.png
  [9]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885078136.png
  [10]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885091400.png
  [11]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885107022.png
  [12]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885117642.png
  [13]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885128502.png
  [14]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885141069.png