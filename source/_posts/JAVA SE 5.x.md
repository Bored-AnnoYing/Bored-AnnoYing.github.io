---
title: JAVA SE 5.x
date: '2018/9/01 17:48:25'
updated: '2018/9/25 22:26:22'
tags:
  - 新特性
  - java
---
>原文：[JDK特性_5.x-10.x.md - \_麒麟之翼\_: https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/](https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/)

## 1. 泛型

C++ 通过模板技术可以指定集合的元素类型，而Java在1.5之前一直没有相对应的功能。

<!-- more -->

一个集合可以放任何类型的对象，相应地从集合里面拿对象的时候我们也不得不对他们进行强制得类型转换。

5.x中引入了泛型，它允许指定集合里元素的类型，这样你可以得到强类型在编译时刻进行类型检查的好处。



## 2. 枚举

    把集合里的对象元素一个一个提取出来。枚举类型使代码更具可读性，理解清晰，易于维护。枚举类型是强类型的，从而保证了系统安全性。而以类的静态字段实现的类似替代模型，不具有枚举的简单性和类型安全性。

- 简单用法
  - Java `Enum`简单的用法一般用于代表一组常用常量，可用来代表一类相同类型的常量值。
- 复杂用法
  - Java为枚举类型提供了一些内置的方法，同时枚举常量还可以有自己的方法。可以很方便的遍历枚举对象。

    

## 3. 注解(Annotation)

> 注释是给人阅读的，而**注解**是给程序阅读的。

注解就相当于一种标记，在程序中加了注解就等于程序加了某种标记。它的本质是一个类。



Java SE5内置了三种标准注解：

- `@Override`表示当前的方法定义将覆盖超类中的方法
- `@Deprecated `使用了注解为它的元素编译器将发出警告，因为注解`@Deprecated`表示不赞成被弃用的代码。
- `@SuppressWarnings`关闭不当编译器警告信息



除了以上3种注解，Java还提供了以下4种注解，专门负责新注解的创建：

- `@Target`表示该注解可以用于什么地方
  - `ElementType.CONSTRUCTOR`可用于构造器
  - `ElementType.FIELD`可用于域声明（包括Enum实例）
  - `ElementType.LOCAL_VARIABLE`可用于局部变量声明
  - `ElementType.METHOD`可用于方法
  - `ElementType.PACKAGE`可用于包
  - `ElementType.PARAMETER`可用于参数
  - `ElementType.TYPE`可用于类、接口（包括注解类型）或 `Enum`
- `@Retention`表示需要在什么级别保存该注解信息
  - `RetentionPolicy.SOURCE`注解只存在于源文件中，编译器后不再存在（被编译器丢弃）
  - `RetentionPolicy.CLASS`注解在Class文件中可用，但在运行时环境中不再存在（被VM丢弃）
  - `RetentionPolicy.RUNTIME`注解在运行时环境中可用（可在运行时通过读取注解信息）
- `@Document `将注解包含在Javadoc中
- `@Inherited `允许子类继承父类中的注解

```java
// 定义一个注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
    String value() defult "";
}
```



## 4. Future模式
Java 5.x 中引入了Future模式（存在于`java.util.concurrent`包中），

`Future` 本身是一种被广泛运用的并发设计模式，可在很大程度上简化需要数据流同步的并发应用开发。在一些领域语言（如Alice ML ）中甚至直接于语法层面支持Future。

Future对象本身可以看作是一个显式的引用，一个对异步处理结果的引用。

由于其异步性质，在创建之初，它所引用的对象可能还并不可用（比如尚在运算中，网络传输中或等待中）。这时，得到Future的程序流程如果并不急于使用Future所引用的对象，那么它可以做其它任何想做的事儿，当流程进行到需要Future背后引用的对象时，可能有两种情况：

- 希望能看到这个对象可用，并完成一些相关的后续流程；如果实在不可用，也可以进入其它分支流程
  - 对于这种情况，可以通过调用`Future.isDone()`判断引用的对象是否就绪，并采取不同的处理
- 无论如何都要一直等待，直到对象可用
  - 这种情况下，则只需调用`get()`或 `get(long timeout, TimeUnit unit)`通过同步阻塞方式等待对象就绪

> 简单而言，`Future`模式可以在连续流程中满足数据驱动的并发需求，既获得了并发执行的性能提升，又不失连续流程的简洁优雅

```java
public class Test {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        executor.shutdown();
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+result.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}

class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i = 0; i<100; i++) {
            sum += i;
        }
        
        return sum;
    }
}
```




## 5. 内省

    内省是Java语言对Bean类属性、事件的一种缺省处理方法。

    例如类A中有属性name,那我们可以通过`getName`和`setName`来得到其值或者设置新的值，通过`getName`/`setName`来访问name属性，这就是默认的规则。

    Java中提供了一套API用来访问某个属性的getter /setter方法，通过这些API可以使你不需要了解这个规则（但你最好还是要搞清楚），这些API存放于包`java.beans`中。

    一般的做法是通过类`Introspector`来获取某个对象的BeanInfo信息，然后通过BeanInfo来获取属性的描述器 `PropertyDescriptor`，通过这个属性描述器就可以获取某个属性对应的getter/setter方法，然后我们就可以通过反射机制来调用这些方法。

     

## 6. 自动装箱与拆箱

### 自动装箱

每当需要一种类型的对象时，这种基本类型就自动地封装到与它相同类型的包装中。

自动装箱，只需将该值赋给一个类型包装器引用，java会自动创建一个对象。



### 自动拆箱

每当需要一个值时，被装箱对象中的值就被自动地提取出来，没必要再去调用`intValue()`和`doubleValue()`方法。

自动拆箱，只需将该对象值赋给一个基本类型即可。



> java中类的包装器有：
>
> - `Double`
> - `Float`
> - `Long`
> - `Integer`
> - `Short`
> - `Character`
> - `Boolean`

  

## 7. For-Each

For-Each循环得加入简化了集合的遍历。假设我们要遍历一个集合对其中的元素进行一些处理。

```java
for (Object o : list)
  System.out.println(o.toString());
}
```



## 8. 静态导入(`import static`)

通过使用 `import static`，就可以不用指定 `Constants` 类名而直接使用静态成员，包括静态方法。



`import xxxx` 和 `import static xxxx` 的区别：

前者一般导入的是类文件，如 `import java.util.Scanner;`

后者一般是导入静态的方法，`import static java.lang.System.out;`



## 9. 可变参数

可变参数的简单语法格式为：`method([argumentList], dataType... argumentName);`