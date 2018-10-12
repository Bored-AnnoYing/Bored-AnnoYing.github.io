---
title: RESTful
date: '2018/1/31 14:35:36'
updated: '2018/9/25 22:28:45'
tags:
  - RESTful
---
# 什么是 REST
> **REST**（**Re**presentational **S**tate **T**ransfer，表现层状态转化），描述了一个架构样式的网络系统。
>
> 它提供了一种软件架构风格、设计风格而**不是**标准，只提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁、更有层次、更易于实现缓存等机制。
>
> 满足这些约束条件和原则的应用程序或者设计就是 **RESTful** 架构。

<!-- more -->

## 资源（Resources）

**REST**的名称 `表现层状态转化` 中，省略了主语。`表现层` 其实指的是 `资源（Resources）` 的**表现层**。

**所谓"资源"，就是网络上的一个实体，或者说是网络上的一个具体信息。**它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。也可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

*所谓 `上网`，就是与互联网上一系列的**资源**互动，调用它的 **URI**。*



## 表现层（Representation）
`资源`是一种信息实体，它可以有多种外在表现形式。

**我们把`资源`具体呈现出来的形式，叫做它的`表现层`（Representation）**

*比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。*

**URI只代表资源的实体，不代表它的形式。**

*严格地说，有些网址最后的 `.html` 后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用 `Accept` 和 `Content-Type` 字段指定，这两个字段才是对 `表现层` 的描述。*



## 状态转化（State Transfer）

访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。

互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。

因此，**如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。**

客户端用到的手段，只能是HTTP协议。

具体来说，就是HTTP协议里面，四个表示操作方式的动词：`GET`、`POST`、`PUT`、`DELETE`

分别对应四种基本操作：获取资源（GET）、新建资源（POST）、更新资源（PUT）、删除资源（DELETE）



# 什么是RESTful架构

- 每个 `URI` 代表一种资源
- 客户端和服务器之间，传递这种资源的某种表现层
- 客户端通过四个HTTP动词，对服务器端资源进行操作，实现**表现层状态转化**




# RESTful API 的设计原则



## 1. URI 表示实体资源

`URI` 表示一种实体资源，因此不应该有动词，应该是名词。（动词应该放在**HTTP**协议中）

如果某些动作是HTTP动词表示不了的，就应该把动作做成一种资源。
比如网上汇款，从账户1向账户2汇款500元。

```
POST /accounts/1/transfer/500/to/2
```

这样的设计是有问题的，应该把动词`transfer`改成名词`transaction`，资源不能是动词，但是可以是一种服务

```
POST /transaction?from=1&to=2&amount=500.00
```


又比如，想要取不同版本的资源时

```
http://www.example.com/app/1.0/foo
... ...
http://www.example.com/app/9.3/foo
```

因为不同的版本，可以理解成同一种资源的不同表现形式，所以应该采用同一个URI。版本号可以在HTTP请求头信息的Accept字段中进行区分。

```
Accept: vnd.example-com.foo+json; version=1.0
... ...
Accept: vnd.example-com.foo+json; version=9.3
```



## 2. HTTP动词

对于资源的具体操作类型，由HTTP动词表示。

> 常用的HTTP动词有下面五个：
>
> - GET（SELECT）：从服务器取出资源（一项或多项，多项的情况下使用名词复数）
> - POST（CREATE）：在服务器新建一个资源
> - PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）
> - PATCH（UPDATE）：在服务器更新部分资源（客户端提供要改变的属性） - 该属性部分Web容器不支持
> - DELETE（DELETE）：从服务器删除资源
>
>
>
> 还有两个不常用的HTTP动词
>
> - HEAD：获取资源的元数据
> - OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的



## 3. 协议

API与用户的通信协议，总是使用 `HTTPs` 协议



## 4. 版本

将API的版本号放入URL，这样做更方便和直观

```
https://api.example.com/v1/
```



## 5. 路径（Endpoint）

路径又称"终点"（endpoint），表示API的具体网址。

在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

举例来说，有个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

```shell
/* 列出所有动物园 */
GET https://api.example.com/v1/zoos

/* 新建一个动物园 */
POST https://api.example.com/v1/zoos

/* 获取某个指定动物园的信息 */
GET https://api.example.com/v1/zoos/{id}

/* 更新某个指定动物园的信息（提供该动物园的全部信息） */
PUT https://api.example.com/v1/zoos/{id}

/* 更新某个指定动物园的信息（提供该动物园的部分信息） */
PATCH https://api.example.com/v1/zoos/{id}

/* 删除某个动物园 */
DELETE https://api.example.com/v1/zoos/{id}

/* 列出某个指定动物园的所有动物 */
GET https://api.example.com/v1/zoos/{zoo_id}/animals

/* 列出某个动物园的所有雇员 */
GET https://api.example.com/v1/zoos/{zoo_id}/employees

/* 列出某个动物园的所有男性雇员(employees表示资源，而gender只是资源的一种属性) */
GET https://api.example.com/v1/zoos/{zoo_id}/employees?gender=male
```



## 6. 过滤信息（Filtering）

记录条数过多的情况下，API应该提供参数，过滤返回结果。

> 一些常见的参数：
>
> - `?limit=10`：指定返回记录的数量
> - `?offset=10`：指定返回记录的开始位置
> - `?page=2&page_size=100`：指定第几页，以及每页的记录数
> - `?sortby=name&order=asc`：指定返回结果按照哪个属性排序，以及排序顺序。
> - `?animal_type_id=1`：指定筛选条件



## 7. 状态码（Status Codes）

服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

> - **200** OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
> - **201** CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
> - **202** Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
> - **204** NO CONTENT - [DELETE]：用户删除数据成功。
> - **400** INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
> - **401** Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
> - **403** Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
> - **404** NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
> - **406** Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
> - **410** Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
> - **422** Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
> - **500** INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
>
>
>
> 状态码的详见 [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)



## 8. 错误处理（Error handling）

如果状态码是4xx，就应该向用户返回出错信息。一般来说，返回的信息中将error作为键名，出错信息作为键值即可。

```
{
    error: "Invalid API key"
}
```



## 9. 返回结果

针对不同操作，服务器向用户返回的结果应该符合以下规范。

- GET /collection：返回资源对象的列表（数组）
- GET /collection/resource：返回单个资源对象
- POST /collection：返回新生成的资源对象
- PUT /collection/resource：返回完整的资源对象
- PATCH /collection/resource：返回完整的资源对象
- DELETE /collection/resource：返回一个空文档





## 10. Hypermedia API

RESTful API最好做到 `Hypermedia`，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。

> 比如，当用户向api.example.com的根目录发出请求，会得到这样一个文档。
>
> ```json
> {
>   "link": {
>       "rel":   "collection https://www.example.com/zoos",
>       "href":  "https://api.example.com/zoos",
>       "title": "List of zoos",
>       "type":  "application/vnd.yourformat+json"
>   }
> }
> ```



## 11. OAuth 2.0

API的身份认证应该使用[OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)框架。



## 12. 数据格式

服务器返回的数据格式，应该尽量使用JSON，避免使用XML



# 参考资料

- [REST best practices](https://bourgeois.me/rest/)
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)