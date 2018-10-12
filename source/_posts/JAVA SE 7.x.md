---
title: JAVA SE 7.x
date: '2018/9/01 17:50:48'
updated: '2018/9/25 22:26:50'
tags:
  - 新特性
  - java
---
>原文：[JDK特性_5.x-10.x.md - \_麒麟之翼\_: https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/](https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/)

## 1. Java™ Platform（NIO.2）

### a. 全新的文件系统API

Java的文件系统主要由`java.io`及`java.nio`两个包内的组件构成。

<!-- more -->

在Java7.x 之前文件操作的API被诟病已久，API对文件的管理非常不便。

Java7.x 推出了全新的 `NIO.2`API以此改变针对文件管理的不便，使得在`java.nio.file`包下使用`Path`、`Paths`、`Files`、`WatchService`、`FileSystem`等常用类型可以很好的简化开发人员对文件管理的编码工作。



> NIO.2更多详细内容可参考
>
> [JAVA1.7 NIO.2 入门，第 2 部分: 文件系统 API](https://blog.csdn.net/zhoujiaxq/article/details/22898335)



####`java.nio.file.Path`

在新的文件I/O中，`Path`是必须掌握的关键类之一，用`Path`对象来实现对文件或者文件夹的操作。(代替之前的`java.io.File`)

Path通常代表文件系统中的位置，例如：

- `C:\workspace\java7developer`（Windows文件系统中的目录）
- `/usr/bin/zip`（Unix文件系统中zip程序的位置）



```java
Path path = Paths.get("C:/test/1/Test.txt");
System.out.println("文件节点数:" + path.getNameCount());     
System.out.println("文件名称:" + path.getFileName());     
System.out.println("文件根目录:" + path.getRoot());     
System.out.println("文件上级关联目录:" + path.getParent());

// File 和 Path 互相转换
Path path = file.toPath();
File file = path.toFile();
```



#### `java.nio.file.FileSystem`

在新的文件I/O中，`FileSystem`代表了一种文件系统。

例如：Unix下的根目录为 `/` ，而Windows下则为`C盘`

```java
FileSystem system = FileSystems.getDefault();
        
String separator = system.getSeparator();
Path path = system.getPath("C:/test/1/", "Test.txt");

FileSystemProvider provider = system.provider();
provider.delete(Paths.get("C:/test/1/", "Test.txt"));
```



#### `java.nio.file.Files`

`Paths`中提供了大量用于简化文件操作的API。

```java
// 获取文件信息
Path path = Paths.get("D:/home/projects/note.txt");
BasicFileAttributes attributes = Files.readAttributes(path,BasicFileAttributes.class);
System.out.println("Creation Time: " + attributes.creationTime());
System.out.println("Last Accessed Time: " + attributes.lastAccessTime());
System.out.println("Last Modified Time: " + attributes.lastModifiedTime());
System.out.println("File Key: " + attributes.fileKey());
System.out.println("isDirectory: " + attributes.isDirectory());
System.out.println("Other Type of File: " + attributes.isOther());
System.out.println("Regular File: " + attributes.isRegularFile());
System.out.println("Symbolic File: " + attributes.isSymbolicLink());
System.out.println("Size: " + attributes.size());

// 操作文件
Path directoryPath = Paths.get("D:/home/sample");
//创建目录
Files.createDirectory(directoryPath);
System.out.println("Directory created successfully!");
Path filePath = Paths.get("D:/home/sample/test.txt");

//创建文件
Files.createFile(filePath);
System.out.println("File created successfully!");

// 拷贝文件
Files.copy(newPath, copiedPath, StandardCopyOption.REPLACE_EXISTING);

// 移动文件
Files.move(newPath, movedPath, StandardCopyOption.REPLACE_EXISTING);

Files.delete(sourceFile);
Files.deleteIfExists(sourceFile);

// 遍历目录
Files.walkFileTree(Paths.get("E:\\Learn\\Java7\\trunk"), new SimpleFileVisitor<Path> {
    @Override  
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {  
        if (file.toString().endsWith(".java")) {
            System.out.println(file.getFileName());
        }
        
        return FileVisitResult.CONTINUE;  
    }  
});
```



#### `java.nio.file.Paths`

`Paths`中包含了用于创建Path对象的静态方法。

```java
Path path = Paths.get("C:/test/1/Test.txt");

Path path = Paths.get("C:/test/1/", "Test.txt");
```



####`java.nio.file.FileSystems`

`FileSystems`中包含用于访问文件系统的类。

```java
FileSystem system = FileSystems.getDefault();

FileSystem system = FileSystems.getFileSystem(URI.create("/"));

FileSystem system = FileSystems.newFileSystem(
    Paths.get("/"),
    ClassLoader.getSystemClassLoader()
);
```



#### `java.nio.file.WatchService`

`WatchService `提供了文件的监测功能，它不仅作为监测服务，还管理着具体的监控细节。

```java
/* 监控目标路径 */  
Path path = Paths.get("C:/watch/test");  
try {  
    /* 创建文件监控对象 */  
    WatchService watchService = FileSystems.getDefault().newWatchService();  

    /* 注册文件监控的所有事件类型 */  
    path.register(watchService, ENTRY_CREATE, ENTRY_DELETE, ENTRY_MODIFY);  

    /* 循环监测文件 */  
    while (true) {  
        WatchKey watchKey = watchService.take();  

        /* 迭代触发事件的所有文件 */  
        for (WatchEvent<?> event : watchKey.pollEvents()) {
            System.out.println(event.context().toString() + event.kind());
        }
        
        if (!watchKey.reset()) {
            return;
        }
    }
}
catch (Exception e) {  
    e.printStackTrace();  
}
```



### b. 选择器和异步IO

NIO的非阻塞I/O是事件驱动的，这就意味着给一个`I/O`通道定义一个选择器（回调或者监听器），然后程序就可以继续运行。

当一个事件发生在这个选择器上时（如接收到一行输入）选择器会“醒来”并且执行。

所有的这些都是通过一个单线程来实现的，这和Java的标准I/O有着显著的差别的。

```java
import java.io.*;
import java.net.*;
import java.nio.*;
import java.nio.channels.*;
import java.util.*;

public class MultiPortEcho {
    private int ports[];
    private ByteBuffer echoBuffer = ByteBuffer.allocate( 1024 );

    public MultiPortEcho( int ports[] ) throws IOException {
        this.ports = ports;
        configureSelector();
    }

    private void configureSelector() throws IOException {
        // Create a new selector
        Selector selector = Selector.open();

        // Open a listener on each port, and register each one with the selector
        for (int i=0; i<ports.length; ++i) {
            ServerSocketChannel ssc = ServerSocketChannel.open();
            ssc.configureBlocking(false);
            ServerSocket ss = ssc.socket();
            InetSocketAddress address = new InetSocketAddress(ports[i]);
            ss.bind(address);

            SelectionKey key = ssc.register(selector, SelectionKey.OP_ACCEPT);
            System.out.println("Going to listen on " + ports[i]);
        }

        while (true) {
            int num = selector.select();

            Set selectedKeys = selector.selectedKeys();
            Iterator it = selectedKeys.iterator();

            while (it.hasNext()) {
                SelectionKey key = (SelectionKey) it.next();

                if ((key.readyOps() & SelectionKey.OP_ACCEPT) == SelectionKey.OP_ACCEPT) {
                    // Accept the new connection
                    ServerSocketChannel ssc = (ServerSocketChannel)key.channel();
                    SocketChannel sc = ssc.accept();
                    sc.configureBlocking(false);

                      // Add the new connection to the selector
                    SelectionKey newKey = sc.register(selector, SelectionKey.OP_READ);
                    it.remove();
                    System.out.println( "Got connection from "+sc );
                }
                else if ((key.readyOps() & SelectionKey.OP_READ) == SelectionKey.OP_READ) {
                    // Read the data
                    SocketChannel sc = (SocketChannel)key.channel();

                    // Echo data
                    int bytesEchoed = 0;
                    while (true) {
                        echoBuffer.clear();
                        int number_of_bytes = sc.read(echoBuffer);

                        if (number_of_bytes <= 0) {
                            break;
                        }

                        echoBuffer.flip();

                        sc.write(echoBuffer);
                        bytesEchoed += number_of_bytes;
                    }

                    System.out.println("Echoed " + bytesEchoed + " from " + sc);
                    it.remove();
                }

            }
        }
    }

    public static void main( String args[] ) throws Exception {
        if (args.length<=0) {
            System.err.println("Usage: java MultiPortEcho port [port port ...]");
            System.exit(1);
        }

        int ports[] = new int[args.length];

        for (int i=0; i<args.length; ++i) {
            ports[i] = Integer.parseInt(args[i]);
        }

        new MultiPortEcho(ports);
    }
}
```





### c. AIO

Java的I/O模型由同步I/O和异步I/O构成。

同步I/O模型包含：阻塞I/O和非阻塞I/O，而在Windows环境下只要调用了IOCP的I/O模型，就是真正意义上的异步I/O。

![](https://www.github.com/Bored-AnnoYing/pic/raw/master/markdownxiaoshujiang/2018-9-25/1537883403017.jpg)



IOCP（Input/Outut Completion Port，输入/输出完成端口）简单来说是一种系统级的高性能异步I/O模型。

应用程序中所有的I/O操作将全部委托给操作系统线程去执行，直至最后通知并返回结果。

Java7.x对IOCP进行了深度封装，这使得开发人员可以使用IOCP API编写高效的分布式应用。
*当然IOCP仅限于使用在Windows平台，因而无法在Linux平台上使用它（Linux平台上可以通过Epoll模拟IOCP实现）*



Java 7 中在 `java.nio.channels` 包中增加四个异步通道：

- AsynchronousSocketChannel
- AsynchronousServerSocketChannel
- AsynchronousFileChannel
- AsynchronousDatagramChannel



> 异步通道更详细内容可参考
>
>  [JAVA1.7 NIO.2 入门，第 1 部分: 异步通道 API](https://blog.csdn.net/zhoujiaxq/article/details/22897625)

    

```java
// 同步文件通道读取
try {  
    Path file = Paths.get("E:\\java\\test.txt");  
  
    FileChannel channel = FileChannel.open(file);  
  
    ByteBuffer buffer = ByteBuffer.allocate(1024);  
    
    channel.read(buffer, channel.size() -13);  
   
    System.out.println((char)buffer.array()[0]);  
}
catch (IOException e) {  
    System.out.println(e.getMessage());
}

// 异步文件通道读取
try {  
    Path file = Paths.get("E:\\java\\test.txt");  
  
    AsynchronousFileChannel channel = AsynchronousFileChannel.open(file);  
  
    ByteBuffer buffer = ByteBuffer.allocate(100_000);  
    Future<Integer> result = channel.read(buffer, 0);  
  
    while (!result.isDone()) {
        System.out.println("done");
    }  
  
    Integer bytesRead = result.get();
    System.out.println("Bytes read [" + bytesRead + "]");
}
catch (IOException | ExecutionException | InterruptedException e) {
    System.out.println(e.getMessage());
}

// 异步文件通道回调式读取
try {  
    Path file = Paths.get("E:\\java\\test.txt");  
    AsynchronousFileChannel channel = AsynchronousFileChannel.open(file);  
  
    ByteBuffer buffer = ByteBuffer.allocate(100_000);  
  
    channel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
        public void completed(Integer result, ByteBuffer attachment) {  
            //System.out.println("read");  
            System.out.println("Bytes read [" + result + "]");       
        }  
  
        public void failed(Throwable exception, ByteBuffer attachment) {  
            System.out.println(exception.getMessage());  
        }  
    });  
    
    // System.out.println("test");  
    Thread.sleep(1000);
}
catch (IOException | InterruptedException e) {
    System.out.println(e.getMessage());
}
```



### d. Channel (通道)

在NIO里，一个通道`channel`可以表示任何可以读写的对象。它的作用是为文件和套接口提供抽象。

NIO通道支持一系列一致的方法，这样就使得编码的时候不需要去特别关心不同的对象，无论它是标准输出，网络连接还是正在使用的通道。

通道的这个特性是继承自Java基本I/O中的流`stream`。

>  流`stream`提供了阻塞式的IO；通道支持异步I/O。

```java
// NetworkChannel
SelectorProvider provider = SelectorProvider.provider();  
try {  
  NetworkChannel socketChannel = provider.openSocketChannel();  
  SocketAddress address = new InetSocketAddress(3080);  
  socketChannel = socketChannel.bind(address);  
  
  Set<SocketOption<?>> socketOptions = socketChannel.supportedOptions();  
  
  System.out.println(socketOptions.toString());  
    
  socketChannel.setOption(StandardSocketOptions.IP_TOS, 3);  
  //System.out.println(socketChannel.getOption(StandardSocketOptions.IP_TOS));  
  Boolean keepAlive = socketChannel.getOption(StandardSocketOptions.SO_KEEPALIVE);  
  // System.out.println(keepAlive);
}
catch (IOException e) {  
  System.out.println(e.getMessage());  
}
```



### d. 内存映射

NIO里对性能提升最显著的是内存映射**memory mapping**。

内存映射是一个系统层面的服务，它把程序里用到的文件的一段当作内存来处理。

内存映射存在很多潜在的影响。在一个更高的层次上，它能够使得文件访问的I/O的性能达到内存访问的速度。

内存映射不仅仅擅长提高I/O的原始速度，同时它也允许多个不同的reader和writer同时处理同一个文件镜像。

这个技术功能强大但是也很危险，不过如果正确使用的话，它会使得你的IO速度提高数倍。

```java
import java.io.RandomAccessFile;
import java.nio.MappedByteBuffer;
import java.nio.channels.FileChannel;

public class mem_map_example {
    private static int mem_map_size = 20 * 1024 * 1024;
    private static String fn = "example_memory_mapped_file.txt";

    public static void main(String[] args) throws Exception {
        RandomAccessFile memoryMappedFile = new RandomAccessFile(fn, "rw");

        //Mapping a file into memory
        MappedByteBuffer out = memoryMappedFile
            .getChannel()
            .map(FileChannel.MapMode.READ_WRITE, 0, mem_map_size);
        //Writing into Memory Mapped File
        for (int i = 0; i < mem_map_size; i++) {
            out.put((byte) 'A');
        }
        
        System.out.println("File '" + fn + "' is now " + mem_map_size + " bytes full.");
        // Read from memory-mapped file.
        for (int i = 0; i < 30 ; i++) {
            System.out.print((char) out.get(i));
        }
        System.out.println("\nReading from memory-mapped file '" + fn + "' is complete.");
    }
}
```



## 2. `Switch`中可以使用字符串

在Java7.x版本之前，switch语句表达式值只能定义`byte`、`short`、`int`和`char`4种类型，且该表达式值只能匹配一个，故不能重复。

Java7.x 允许`switch`定义一种全新的表达式值，`String`类型。

```java
String str = ...;
switch(str) {  
    case "a":
        System.out.println("a");
        break;
        
    case "b":
        System.out.println("b");
}
```



## 3. 钻石运算符`<>`

在Java7.x中，允许使用钻石运算符`<>`来做泛型实例化类型的自动推断。也就是说只需要在声明时标注泛型类型，实现时无需重复标注。

```java
Map<String, List<String>> anagrams = new HashMap<>();
```



## 4. 自动资源回收（`try-with-resources`语句）

早在7.x版本之前，某些可回收资源比如：I/O链接、DB连接、TCP/UDP连接。开发人员都需要在使用后对其进行手动关闭，如果不关闭或者忘记关闭这些资源，就会长期霸占JVM内部的资源，极大程度上影响了JVM的资源分配。就像内存管理一样，开发人员梦寐以求的就是希望有一天再也无需关注繁琐的资源管理（资源创建、资源就绪、资源回收）。值得庆幸的是7.x为我们带来了一次彻头彻尾改变，我们将再也不必以手动管理我们的资源。

早在Java5.x的时候，Java API为开发人员提供了一个`Closeable`接口。该接口中包含一个`close()`方法，允许所有可回收资源的类型对其进行重写实现。7.x版本中几乎所有的资源类型都实现了`Closeable`接口，并重写了`close()`方法。也就是说所有可回收的系统资源，我们将再不必每次使用完后调用`close()`方法进行资源回收，这一切全部交接给自动资源管理器去做即可。

```java
try (BufferedReader br = new BufferedReader(new FileReader(path)) {
    return br.readLine();
}
```



## 5. catch表达式

在 Java7.x 之前有2种捕获异常的方式。

第一种是采用定义多个catch代码块，另外一种则是直接使用Exception（可恢复性异常超类）进行捕获。



Java7.x 允许在catch表达式内部使用`|`运算符匹配多个异常类型，当触发异常时，异常类型将自动进行类型匹配操作。

```java
try {
   ......
}
catch ( ClassNotFoundException | SQLException ex ) {
   ex.printStackTrace();
}
```



## 6. 字面量书写优化

```java
// 支持字面量使用下划线
int one_million = 1_000_000;

// 支持二进制字面量使用下划线
int binary = 0b1001_1001;
```



## 7. `ClassLoader`架构调整

Java 7.x 在原有`ClassLoader`架构的基础之上，进行了一些细微调整。

在早期开发人员如果想要实现自定义类装载器，恐怕只能实现`ClassLoader`类型并重写其`findClass()`方法。

但由于`findClass()`方法是按照串行结构的方式执行，或许是出于对性能和安全的考虑。

Java 7.x 提供了一个拥有并行执行能力的增强实现，这样一来自定义类装载器便可以通过异步方式对类型进行装载。