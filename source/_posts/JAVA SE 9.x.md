---
title: JAVA SE 9.x
date: '2018/9/01 17:58:34'
updated: '2018/9/25 22:26:03'
tags:
  - 新特性
  - java
---
>原文：[JDK特性_5.x-10.x.md - \_麒麟之翼\_: https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/](https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/)

## 1. Java平台模块化系统

模块化系统是Java 9 最大的特性。

它引入了一种新的Java编程组件模块，它是一个命名的、自描述的代码和数据集合。

<!-- more -->

Java 9代号叫`Modularity`，提供了类似于OSGI框架的功能，模块之间存在相互的依赖关系，可以导出一个公共的API，并且隐藏实现的细节。

同时，当代码库越来越大，创建复杂，盘根错节的“意大利面条式代码”的几率呈指数级的增长。

这时候就得面对两个基础的问题：

- 很难真正地对代码进行封装, 而系统并没有对不同部分（也就是 JAR 文件）之间的依赖关系有个明确的概念。
- 每一个公共类都可以被类路径之下任何其它的公共类所访问到, 这样就会导致无意中使用了并不想被公开访问的 API。

> 此外，类路径本身也存在问题: 你怎么知晓所有需要的 JAR 都已经有了, 或者是不是会有重复的项呢?

模块系统把以上问题都给解决了。



### 模块描述器

模块化的 JAR 文件都包含一个额外的模块描述器。

在这个模块描述器中, 对其它模块的依赖是通过 `requires`来表示的。

另外, `exports`语句控制着内部的哪些包是可以被其它模块访问到的。

所有不被导出的包默认都封装在模块的里面。

```java
// 示例 'module-info.java'
module blog {
    exports com.pluralsight.blog;
    requires cms;
}
```



## 2. 响应式流（Reactive Streams）

> 响应式流从2013年开始，作为提供**非阻塞背压**的异步流处理标准的倡议。
>
> 它旨在解决处理元素流的问题：如何将元素流从发布者传递到订阅者，而不需要发布者阻塞或订阅者有无限制的缓冲区或丢弃。

Java SE 9 中引入了响应式流的 API，在`java.util.concurrent.Flow` 类中封装了以下四个接口：

- `Flow.Processor `
- `Flow.Publisher`
- `Flow.Subscriber`
- `Flow.Subscription`

> 响应式流更多详细内容可以参考以下文章：
>
> - [Java 9 揭秘（17. Reactive Streams）](https://www.cnblogs.com/IcanFixIt/p/7245377.html)



## 3. HTTP/2 Client API

Java 9 提供了一个支持`HTTP/1.1`和`HTTP/2`的HTTP/2 Client API。 该API旨在最终取代旧的API。

新API还包含使用WebSocket协议开发客户端应用程序的类和接口。

这个迟到的特性用于代替老旧的 `HttpURLConnection API`，并提供对 WebSocket 和 HTTP/2 的支持。



新的HTTP/2客户端API与现有的API相比有以下几个好处：

- 在大多数常见情况下，学习和使用简单易用
- 它提供基于事件的通知。 *例如，当收到首部信息，收到正文并发生错误时，会生成通知*
- 它支持服务器推送，这允许服务器将资源推送到客户端，而客户端不需要明确的请求
  - 它使得与服务器的WebSocket通信设置变得简单
- 它支持HTTP/2和HTTPS/TLS协议
- 它同时工作在同步（阻塞模式）和异步（非阻塞模式）模式下

```java
// HTTP 请求/响应模型
HttpRequest request = HttpRequest.newBuilder(URI.create("http://www.google.com"))
              .header("User-Agent","Java")
              .GET()
              .build();

HttpClient client = HttpClient.newHttpClient();

HttpResponse<String> response = client.send(request, HttpResponse.BodyHandler.asString());

// HTTP 流

// HTTP 服务端推送
```

> HTTP/2更多详细内容可以参考以下文章：
>
> - [Java 9 揭秘（14. HTTP/2 Client API）](http://www.cnblogs.com/IcanFixIt/p/7229611.html)

    

## 4. 改进的 Stream API

长期以来，Stream API 都是 Java 标准库最好的改进之一, 通过这套 API 可以在集合上建立用于转换的申明管道。

在 Java 9 中, Stream 接口中添加了 4 个新的方法：

- `default Stream<T> dropWhile(Predicate<? super T> predicate) `

  - 对于有序流，`dropWhile()`方法返回流的元素，从指定`predicate`为`true`的起始处丢弃元素。

  ```java
  IntStream.of(1, 2, 3, 4, 5, 6, 7)
      .dropWhile(i -> i < 5)
      .forEach(System.out::println);  // 5, 6, 7
  ```

  - 对于无序流，`dropWhile()`方法的行为是非确定性的。
    - 它可以选择删除匹配`predicate`的任何元素子集。
    - 当前的实现从匹配元素开始丢弃匹配元素，直到找到不匹配的元素。
  - `dropWhile()`方法有两种极端情况
    - 如果第一个元素与`predicate`不匹配，则该方法返回原始流
    - 如果所有元素与`predicate`匹配，则该方法返回一个空流

- `default Stream<T> takeWhile(Predicate<? super T> predicate)`

  - `takeWhile()`方法的工作方式`与dropWhile()`方法相同，只是它从流的起始处返回匹配的元素，而丢弃其余的

  ```java
  IntStream.of(1, 2, 3, 4, 5, 6, 7)
      .takeWhile(i -> i < 5)
      .forEach(System.out::println);  // 1, 2, 3, 4
  ```

- `static <T> Stream<T> ofNullable(T t)`

  - 如果元素为非空，则`ofNullable(T t)`方法返回包含指定元素的单个元素的流
  -  如果指定的元素为空，则返回一个空的流
  - 在流处理中使用`flatMap()`方法时，此方法非常有用

  ```java
  // 需要排除Map中可能存在的空值null, 而返回剩余的非空值的集合
  Map<Integer, String> map = new HashMap<>();
  map.put(1, "One");
  map.put(2, "Two");
  map.put(3, null);
  map.put(4, "Four");
  
  // Java 8
  Set<String> nonNullvalues = map.entrySet()
             .stream()          
             .flatMap(e ->  e.getValue() == null ? Stream.empty() : Stream.of(e.getValue()))
             .collect(toSet());  // ["One", "Two", "Four"]
  
  // Java 9
  Set<String> nonNullvalues = map.entrySet()
             .stream()          
             .flatMap(e -> Stream.ofNullable(e.getValue()))
             .collect(toSet());  // ["One", "Two", "Four"]
  ```

- `static <T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next)`

```java
for (int i = 1; i < 5; i++) {
    System.out.print(i);  // 1, 2, 3, 4, 5
}

IntStream.iterate(1, i -> i < 5, i -> i + 1)
    .forEach(System.out::println);  // 1, 2, 3, 4, 5
```



> 除此之外，`Optional` 和 `Stream` 之间的结合也得到了改进。
>
> 现在可以通过 `Optional` 的新方法 `stream()` 将一个 `Optional` 对象转换为一个(可能是空的) `Stream` 对象：
>
> ```java
> Stream<Integer> s = Optional.of(1).stream();
> ```



同时，在`Collectors `类中新增了两个方法：

- `filtering()`
  - 返回在收集元素之前应用过滤器的收集器
    - 如果指定的predicate对于元素返回true，则会收集元素；
    - 否则，元素未被收集

  ```java
  // 按部门对员工进行分组, 并在分组时附加限制条件: 分组内的员工必须满足 Salary > 1900
  
  // Java 8
  Map<String, List<Employee>> empSalaryGt1900GroupedByDept = Employee.employees()
                  .stream()
                  .filter(e -> e.getSalary() > 1900)
                  .collect(
                      groupingBy(Employee::getDepartment, toList())
                  );
  
  System.out.println(empSalaryGt1900GroupedByDept);
  /*
   * 输出结果
   * {
   *   HR=[[Li, HR, 1950.89, [Wu, Lao]]],
   *   IT=[[Manuel, IT, 2001.99, [English, German]]]
   * }
   *
   * 由于Sales部门下所有人都不满足条件
   * 因此, Java 8中提前进行的filter会把所有Sales部门的记录排除掉
   */
  
  // Java 9
  Map<String, List<Employee>> empGroupedByDeptWithSalaryGt1900 = Employee.employees()
                     .stream()
                     .collect(
                          groupingBy(
                                 Employee::getDepartment,
                              filtering(e -> e.getSalary() > 1900.00, toList())
                          )
                      );
  
  System.out.println(empGroupedByDeptWithSalaryGt1900);
  /* 输出结果
   * {
   *   Sales=[],
   *   HR=[[Li, HR, 1950.89, [Wu, Lao]]],
   *   IT=[[Manuel, IT, 2001.99, [English, German]]]
   * }
   *
   * Java 9中是在进行收集的时候应用该条件
   * 因此, 会完成对部门的分类后, 对收集器应用条件, 最终Sales部门的记录也存在, 只是内容为空
   */
  ```

- `flatMapping() `
  - 返回在收集元素之前应用扁平映射方法的收集器
    - 指定的扁平映射方法被应用到流的每个元素，并且从扁平映射器返回的流的元素的累积

```java
// 获取按部门分组后, 该部门下所有人所讲语言的集合

// Java 8
Map<String, Set<List<String>>> langByDept = Employee.employees()
                .stream()
                .collect(
                    groupingBy(
                        Employee::getDepartment,
                        mapping(Employee::getSpokenLanguages, toSet())
                    )
                );

System.out.println(langByDept);
/*
* 输出结果
* {
*   Sales=[[English, French], [Spanish, Wu]],
*   HR=[[Wu, Lao]],
*   IT=[[English, German], [English]]
* }
*
* mapping 后集合中的元素是List<String>,
* 没有办法在这个时候把List<String>扁平化, 仅得到 Set<String>
*/


// Java 9
Map<String, Set<String>> langByDept2 = Employee.employees()
                .stream()
                .collect(
                    groupingBy(
                        Employee::getDepartment,
                        flatMapping(e -> e.getSpokenLanguages().stream(), toSet())
                    )
                );

System.out.println(langByDept2);
/*
* 输出结果
* {
*   Sales=[English, French, Spanish, Wu],
*   HR=[Wu, Lao],
*   IT=[English, German]
* }
*
* 与Java 8中的mapping对比, flatMapping 在映射的同时时提供了扁平化操作
* 因此, 最终把Set<List<String>>扁平化为Set<String>
*/
```

    

## 5. 集合工厂方法

Java 9.x中新增了几种集合工厂方法（类似于Guava的`Lists`、`Sets`、`Maps`等）：

```java
Set<Integer> ints = Set.of(1, 2, 3);
List<String> strings = List.of("first", "second");
```

> 在创建后，这些集合是不可变的，继续添加元素到这些集合会导致`UnsupportedOperationException`

    

## 6. 私有接口方法

Java 8 为我们带来了接口的默认方法。 接口现在也可以包含行为，而不仅仅是方法签名。 但是，如果在接口上有几个默认方法，代码几乎相同，会发生什么情况？ 通常，您将重构这些方法，调用一个可复用的私有方法。 但默认方法不能是私有的。 将复用代码创建为一个默认方法不是一个解决方案，因为该辅助方法会成为公共API的一部分。

Java 9中现在支持向接口添加私有辅助方法：

```java
public interface MyInterface {

    void normalInterfaceMethod();

    default void interfaceMethodWithDefault() {
        init();
    }

    default void anotherDefaultMethod() {
        init();
    }

    // This method is not part of the public API exposed by MyInterface
    private void init() {
        System.out.println("Initializing");
    }
}
```



## 7. 多版本兼容 JAR

当一个新版本的 Java 出现的时候，你的库用户要花费数年时间才会切换到这个新的版本。

这就意味着库得去向后兼容你想要支持的最老的 Java 版本 (许多情况下就是 Java 6 或者 7)。

这实际上意味着未来的很长一段时间，你都不能在库中运用 Java 9 所提供的新特性。

幸运的是，多版本兼容 JAR 功能能让你创建仅在特定版本的 Java 环境中运行库程序时选择使用的 class 版本：

```
multirelease.jar
├── META-INF
│   └── versions
│       └── 9
│           └── multirelease
│               └── Helper.class
├── multirelease
    ├── Helper.class
    └── Main.class
```

在上述场景中， `multirelease.jar` 可以在 Java 9 中使用,  不过 Helper 这个类使用的不是顶层的 `multirelease.Helper` 这个 class, 而是处在`META-INF/versions/9`下的`multirelease.Helper`

这是特别为 Java 9 准备的 class 版本，可以运用 Java 9 所提供的特性和库。

同时，在早期的 Java 诸版本中使用这个 JAR 也是能运行的，因为较老版本的 Java 只会看到顶层的这个 Helper 类。

  

## 8. Process API

### ProcessHandle

Java 9向Process API添加了一个名为`ProcessHandle`的接口。

`ProcessHandle`接口的实例标识一个本地进程(无论该进程是由当前Java程序启动还是以其他方式启动的); 它允许查询进程状态并管理进程。

```java
// 获取当前正在执行代码的Java进程的句柄
// 注意: 不能调用当前进程的 destroy() 或 destroyForcibly() 方法来杀死当前进程
ProcessHandle current = ProcessHandle.current();

// 获取当前进程的所有直接子进程的快照
Stream<ProcessHandle> childrenProcess = current.children();

// 获取操作系统中当前进程可见的所有进程的快照
Stream<ProcessHandle> allProcesses = ProcessHandle.allProcesses();

// 根据进程pid获取进程
long pid = 10010;
Optional<ProcessHandle> processHandle = ProcessHandle.of(pid);

processHandle.ifPresent(handle -> {
    System.out.println(handle.getPid());  // 获取进程pid
    System.out.println(handle.isAlive());  // true: 进程未终止,
});

allProcesses.findFirst().get();
```

### ProcessHandle.Info

```java
ProcessHandle handle = ProcessHandle.current();

// Get other process info
ProcessHandle.Info info = handle.info();
String command = info.command().orElse("");
String[] args = info.arguments().orElse(new String[]{});
String commandLine = info.commandLine().orElse("");

Instant instant = info.startInstant().orElse(Instant.now());
ZonedDateTime startTime = instant.atZone(ZoneId.systemDefault());
Duration duration = info.totalCpuDuration().orElse(Duration.ZERO);
String owner = info.user().orElse("Unknown");

// Print the process details
System.out.printf("PID: %d%n", handle.getPid());        
System.out.printf("IsAlive: %b%n", handle.isAlive());
System.out.printf("Children Count: %d%n", handle.children().count());

System.out.printf("Command: %s%n", command);
System.out.printf("Arguments: %s%n", Arrays.toString(args));
System.out.printf("CommandLine: %s%n", commandLine);
System.out.printf("Start Time: %s%n", startTime);
System.out.printf("CPU Time: %s%n", duration);
System.out.printf("Owner: %s%n", owner);
```

> Process 更多详细内容可以参考以下文章：
>
> - [Java 9 揭秘（12. Process API 更新）](http://www.cnblogs.com/IcanFixIt/p/7214359.html)



## 9. 轻量级的Json API



## 10. JShell : 交互式 Java REPL

许多语言已经具有交互式编程环境，Java 现在加入了这个俱乐部。

现在可以从控制台启动 `jshell` ，并直接启动输入和执行 Java 代码。

`jshell` 的即时反馈使它成为探索 API 和尝试语言特性的好工具。

 交互式 shell 还可以提供良好的教学环境以及提高生产力。



## 11. 改进的 Javadoc

Javadoc 现在支持在 API 文档中的进行搜索。

另外，Javadoc 的输出现在符合兼容 HTML5 标准。

此外，每个 Javadoc 页面都包含有关 JDK 模块类或接口来源的信息。



## 12. JVM新特性

- 使用`G1`做为默认回收器
- 去掉了jdk8弃用的垃圾回收器组合
  - DefNew + CMS
  - ParNew + SerialOld
  - Incremental CMS
- 去掉了一些jvm参数
  - `-Xincgc`
  - `-XX:+CMSIncrementalMode`
  - `-XX:+UseCMSCompactAtFullCollection`
  - `-XX:+CMSFullGCsBeforeCompaction`
  - `-XX:+UseCMSCollectionPassing`
- 统一GC日志
- 废弃CMS