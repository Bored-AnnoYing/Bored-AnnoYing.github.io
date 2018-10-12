---
title: 创建 IP安全策略来屏蔽端口
date: '2017/9/28 15:37:19'
updated: '2018/9/25 22:45:35'
tags:
  - IP安全策略
  - 屏蔽端口
---
        在网络上传输数据的时候，通过创建IP安全策略，利用点到点的安全模型，能够安全有效地把源计算机的数据传输到目标计算机。

创建IP安全策略

一、单击“开始”-“控制面板”-“管理工具”。

![管理工具][1]

<!-- more -->

二、在“管理工具”页面，选择“本地安全策略”，双击打开。

![本地策略][2]

三、在弹出的“本地安全设置”对话框中，选择“IP 安全策略，在本地计算机”。

![IP策略安全][3]

四、在右边窗格的空白位置右击鼠标，弹出快捷菜单，选择“创建 IP 安全策略”。

![创建IP安全策略][4]

五、在弹出的“IP 安全策略”向导对话框中，单击“下一步”按钮。

![下一步][5]

六、在出现的“IP安全策略名称”界面中，输入新IP安全策略的名称，单击“下一步”按钮。

![下一步][6]

七、在出现的“安全通讯请求”界面中，把“激活默认相应规则”左边的勾去掉，点击“下一步”。

![下一步][7]

八、在出现的“正在完成IP安全策略向导”界面中，去掉“编辑属性”前的勾，单击“完成”按钮。

![完成][8]

九、在“本地安全设置”对话框，选择IP安全策略“ITCSO”，右键单击，选择“属性”。

![属性][9]

十、在弹出的“ITCSO属性”对话框中，把“使用添加向导”左边的勾去掉，然后单击“添加”按钮添加新的规则。

![添加规则][10]

十一、在弹出的“新规则属性”对话框“IP筛选器列表”选项卡中，单击“添加”按钮。

![添加IP筛选列表][11]

十二、在弹出的“IP筛选器列表”对话框中，单击“添加”按钮。

![添加][12]

十三、在出现的“筛选器属性”对话框“寻址”选项卡中，把源地址设为“任何IP地址”，目标地址设为“我的IP地址”。

![设置地址][13]

十四、在出现的“筛选器属性”对话框“协议”选项卡中，在“选择协议类型”的下拉列表中选择“TCP”，然后在“到此端口”下的文本框中输入“135”，点击“确定”按钮，这样就添加了一个屏蔽 TCP 135（RPC）端口的筛选器。

![设置需要屏蔽的端口][14]

十五、依照以上步，添加139、3389、445、137端口筛选器，单击“确定”按钮。

![添加端口][15]

十六、返回到“新规则属性”对话框，在“IP筛选器列表”选项卡中，单击“新IP筛选器列表”前的圆点。

![筛选器列表][16]

十七、在“新规则属性”对话框，“IP筛选器操作”选项卡中，单击 “新筛选器操作”前的圆点，去掉“使用‘添加向导'”前的小勾，单击“添加”按钮。

![添加][17]

十八、在出现的“新筛选器操作（1）属性”对话框“安全措施”选项卡中，选择“阻止”，单击“确定”按钮。

![确定][18]

十九、进入“新规则属性”对话框，点击“新筛选器操作（1）”，其左边的圆圈会加了一个点，表示已经激活，点击“关闭”按钮，关闭对话框。

![关闭][19]

二十、返回到“ITCSO属性”对话框，在“新IP筛选器列表”左边打钩，按“确定”按钮关闭对话框。

![][20]

二十一、返回到 “本地安全策略”窗口，用右击新添加的 IP 安全策略“ITCSO”，然后选择“指派”。

![指派][21]

二十二、设置完成，重新启动电脑后，电脑中上述网络端口就被关闭了，病毒和黑客再也不能连接这些端口，从而保护了你的电脑。


  [1]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885230524.png
  [2]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885245378.png
  [3]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885256614.png
  [4]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885266941.png
  [5]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885278624.png
  [6]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885289451.png
  [7]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885301076.png
  [8]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885312672.png
  [9]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885323537.png
  [10]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885337262.png
  [11]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885350056.png
  [12]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885362918.png
  [13]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885374710.png
  [14]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885387219.png
  [15]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885399118.png
  [16]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885411658.png
  [17]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885422558.png
  [18]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885434470.png
  [19]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885444759.png
  [20]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885456423.png
  [21]: https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537885467208.png