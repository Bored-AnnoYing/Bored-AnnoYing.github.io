---
title: 'URI, URL, URN'
date: '2018/9/29 11:48:35'
updated: '2018/9/29 11:49:08'
tags:
  - URI
  - URL
  - URN
---
## 定义
>**URI**: 统一资源标识符（Uniform Resource Identifier）：是一个用于标识某一互联网资源名称的字符串。 该种标识允许用户对网络中（一般指万维网）的资源通过特定的协议进行交互操作。

>**URL**:  统一资源定位符（Uniform Resource Locator）：有时也被俗称为网页地址（网址）。如同在网络上的门牌，是因特网上标准的资源的地址（Address）。

>**URN**: 统一资源名称（Uniform Resource Name）：是统一资源标识（URI）的历史名字，它使用urn:作为URI scheme。

<!-- more -->

## URL和URN的关系
URI可被视为定位符（URL），名称（URN）或两者兼备。统一资源名（URN）如同一个人的名称，而统一资源定位符（URL）代表一个人的住址。换言之，URN定义某事物的身份，而URL提供查找该事物的方法。

用于标识唯一书目的ISBN系统是一个典型的URN使用范例。例如，`ISBN 0-486-27557-4`( urn:ISBN 0-486-27557-4 )无二义性地标识出莎士比亚的戏剧《罗密欧与朱丽叶》的某一特定版本。为获得该资源并阅读该书，人们需要它的位置，也就是一个URL地址。在类Unix操作系统中，一个典型的URL地址可能是一个文件目录，例如`file:///home/username/RomeoAndJuliet.pdf`。该URL标识出存储于本地硬盘中的电子书文件。因此，URL和URN有着互补的作用。

## URL
URL是URI的子集, 除了确定一个资源,还提供一种定位该资源的主要访问机制(如其网络“位置”)。

URL的标准格式如下：
```http
协议类型:[//服务器地址[:端口号]][/资源层级UNIX文件路径]文件名[?查询][#片段ID]
```
超文本传输协议（HTTP）的统一资源定位符将从因特网获取信息的五个基本元素包括在一个简单的地址中：
1. 传送协议。Data URI scheme
2. 层级URL标记符号(为[//],固定不变)
3. 访问资源需要的凭证信息（可省略）
4. 服务器。（通常为域名，有时为IP地址）
5. 端口号。（以数字方式表示，若为HTTP的默认值“:80”可省略）
6. 路径。（以“/”字符区别路径中的每一个目录名称）
7. 查询。（GET模式的窗体参数，以“?”字符为起点，每个参数以“&”隔开，再以“=”分开参数名称与数据，通常以UTF8的URL编码，避开字符冲突的问题）
8. 片段。以“#”字符为起点

## URI
URI文法由URI协议名（例如“http”，“ftp”，“mailto”或“file”），一个冒号，和协议对应的内容所构成。特定的协议定义了协议内容的语法和语义，而所有的协议都必须遵循一定的URI文法通用规则，亦即为某些专门目的保留部分特殊字符。URI文法同时也就各种原因对协议内容加以其他的限制，例如，保证各种分层协议之间的协同性。百分号编码也为URI提供附加信息。

通用URI的格式(URL完成格式)如下：
```http
scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
协议类型:[//[访问资源需要的凭证信息@]服务器地址[:端口号]][/资源层级UNIX文件路径]文件名[?查询][#片段ID]
```
例如：

![](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-29/1538189749089.png)

- http://en.wikipedia.org/wiki/URI#Examples_of_URI_references （"http" 指定协议名, `en.wikipedia.org`是“典据”, `/wiki/URI`是指向英文维基页面的“路径”，而`#Examples_of_URI_references`是指向英文维基页面相应片段的“片段”。)
- http://example.org/absolute/URI/with/absolute/path/to/resource.txt
- //example.org/scheme-relative/URI/with/absolute/path/to/resource.txt
- /relative/URI/with/absolute/path/to/resource.txt
- relative/path/to/resource.txt
- ../../../resource.txt
- ./resource.txt#frag01
- resource.txt
- #frag01
- (空字符串)

## URN
URN的语法[3]用巴科斯范式来写是：
```http
<URN> ::= "urn:" <NID> ":" <NSS>
```
解析出来是：
```http
urn:<NID>:<NSS>
```
开头的`urn:`序列大小写不敏感的。`<NID>`是命名空间标识，它是一个“命名空间特定”的字符串，决定了如何解释`<NSS>`的句法。统一资源名称的功能需求在RFC 1737中描述

### 命名空间
为了确保URN命名空间的全局唯一性，URN的标识（NID）必须在IANA注册。已注册命名空间可能是“正式”或“非正式”的。此中有一个例外——“试验性命名空间”无需注册。

正式命名空间是互联网用户预期能通过公开而的一类命名空间，[5]因此会受到许多限制。它必须：
- 不能和已经注册的NID重复
- 不能由`x-`（见下“试验命名空间”）开头
- 不能由`urn-`开头
- 不能有任何`XY-`形式的开头，其中XY任意两个ASCII字母的组合
- 比两个字母更长

例如：
```http
urn:isbn:0451450523
urn:isan:0000-0000-9E59-0000-O-0000-0000-2
urn:issn:0167-6423
urn:ietf:rfc:2648
urn:mpeg:mpeg7:schema:2001
urn:oid:2.16.840
urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66
urn:nbn:de:bvb:19-146642
urn:lex:eu:council:directive:2010-03-09;2010-19-UE
```