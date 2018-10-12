---
title: JAVA SE 8.x
date: '2018/9/01 17:57:32'
updated: '2018/9/25 22:26:09'
tags:
  - 新特性
  - java
---
>原文：[JDK特性_5.x-10.x.md - \_麒麟之翼\_: https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/](https://note.youdao.com/share/?id=7e92c66eb7e005b3c882a3d6b62a71aa&type=note#/)

## 1. 函数式接口

函数式接口是Java 8.x中引入的最主要的新特性。

<!-- more -->

所谓的函数式接口，当然首先是一个**接口**，然后就是在这个接口里面只能有**一个抽象方法**。

这种类型的接口也称为**SAM**接口（即**S**ingle **A**bstract **M**ethod interfaces）。

> **函数式接口语法定义**
>
> 一个只包含唯一的抽象方法的接口，会被默认视为函数式接口的声明。(也可通过`@FunctionalInterface`显示声明)
>
>
>
> **注意点**
>
> 1. *函数式接口不代表只能定义一个方法，它可以包含数个默认实现或静态方法*
> 2. *函数式接口也是接口，还可以按原先接口的使用方式正常使用*
> 3. *由接口继承导致的子接口中不满足仅包含唯一的抽象方法的定义时，子接口就不再是函数式接口*
> 4. *通过`@FunctionalInterface`显示定义的函数式接口，当不再满足定义时，会生成编译错误。*
> 5. *本质上来说，加不加`@FunctionalInterface`对于接口是不是函数式接口没有影响，只是多了一个编译期的检查*

```java
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);

    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
   
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }
   
    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
}
```



Java 8.x 中在`java.util.function `包下新增了以下通用的函数式接口：

- **Predicate**

| 序号 | 接口              | 描述                                         |
| ---- | ----------------- | -------------------------------------------- |
| 1    | Predicate\<T>     | 接受一个输入参数，返回一个布尔值结果         |
| 2    | DoublePredicate   | 代表一个拥有double值参数的boolean值方法      |
| 3    | IntPredicate      | 接受一个int输入参数，返回一个布尔值的结果    |
| 4    | LongPredicate     | 接受一个long输入参数，返回一个布尔值类型结果 |
| 5    | BiPredicate<T, U> | 代表了一个两个参数的boolean值方法            |

- **Function**

| 序号 | 接口                     | 描述                                               |
| ---- | ------------------------ | -------------------------------------------------- |
| 1    | Function<T, R>           | 接受一个输入参数，返回一个结果                     |
| 2    | DoubleFunction\<R>       | 代表接受一个double值参数的方法，并且返回结果       |
| 3    | IntFunction\<R>          | 接受一个int类型输入参数，返回一个结果              |
| 4    | LongFunction\<R>         | 接受一个long类型输入参数，返回一个结果             |
| 5    | ToDoubleFunction\<T>     | 接受一个输入参数，返回一个double类型结果           |
| 6    | ToIntFunction\<T>        | 接受一个输入参数，返回一个int类型结果              |
| 7    | ToLongFunction\<T>       | 接受一个输入参数，返回一个long类型结果             |
| 8    | DoubleToIntFunction      | 接受一个double类型输入，返回一个int类型结果。      |
| 9    | DoubleToLongFunction     | 接受一个double类型输入，返回一个long类型结果       |
| 10   | IntToDoubleFunction      | 接受一个int类型输入，返回一个double类型结果        |
| 11   | IntToLongFunction        | 接受一个int类型输入，返回一个long类型结果          |
| 12   | LongToDoubleFunction     | 接受一个long类型输入，返回一个double类型结果       |
| 13   | LongToIntFunction        | 接受一个long类型输入，返回一个int类型结果          |
| 14   | BiFunction<T, U, R>      | 代表了一个接受两个输入参数的方法，并且返回一个结果 |
| 15   | ToDoubleBiFunction<T, U> | 接受两个输入参数，返回一个double类型结果           |
| 16   | ToIntBiFunction<T, U>    | 接受两个输入参数，返回一个int类型结果              |
| 17   | ToLongBiFunction<T, U>   | 接受两个输入参数，返回一个long类型结果             |
- **Supplier**

| 序号 | 接口            | 描述                             |
| ---- | --------------- | -------------------------------- |
| 1    | Supplier\<T>    | 无参数，返回一个结果             |
| 2    | DoubleSupplier  | 代表一个double值结构的提供方     |
| 3    | IntSupplier     | 无参数，返回一个int类型结果      |
| 4    | LongSupplier    | 无参数，返回一个结果long类型的值 |
| 5    | BooleanSupplier | 代表了boolean值结果的提供方      |
- **Consumer**

| 序号 | 接口                  | 描述                                                   |
| ---- | --------------------- | ------------------------------------------------------ |
| 1    | Consumer\<T>          | 代表了接受一个输入参数并且无返回的操作                 |
| 2    | DoubleConsumer        | 代表一个接受double值参数的操作，并且不返回结果         |
| 3    | IntConsumer           | 接受一个int类型的输入参数，无返回值                    |
| 4    | LongConsumer          | 接受一个long类型的输入参数，无返回值                   |
| 5    | ObjDoubleConsumer\<T> | 接受一个object类型和一个double类型的输入参数，无返回值 |
| 6    | ObjIntConsumer\<T>    | 接受一个object类型和一个int类型的输入参数，无返回值    |
| 7    | ObjLongConsumer\<T>   | 接受一个object类型和一个long类型的输入参数，无返回值   |
| 8    | BiConsumer<T, U>      | 代表了一个接受两个输入参数的操作，并且不返回任何结果   |

- **Operator**

| 序号 | 接口 | 描述          |
| ---- | -------------------- | ------------------------------------------------------------ |
| 1    | BinaryOperator\<T>   | 代表了一个作用于于两个同类型操作符的操作，并且返回了操作符同类型的结果 |
| 2    | DoubleBinaryOperator | 代表了作用于两个double值操作符的操作，并且返回了一个double值的结果 |
| 3    | DoubleUnaryOperator  | 接受一个参数同为类型double,返回值类型也为double              |
| 4    | IntBinaryOperator    | 接受两个参数同为类型int,返回值类型也为int                    |
| 5    | IntUnaryOperator     | 接受一个参数同为类型int,返回值类型也为int                    |
| 6    | LongBinaryOperator   | 接受两个参数同为类型long,返回值类型也为long                  |
| 7    | LongUnaryOperator    | 接受一个参数同为类型long,返回值类型也为long                  |
| 8    | UnaryOperator\<T>    | 接受一个参数为类型T,返回值类型也为T                          |

  

## 2. Lambda 表达式

`lambda` 表达式又被成为“闭包”，它提供了轻量级的语法来表示一个函数式接口。

> lambda 表达式的语法由以下3部分组成：
>
> - 参数列表
> - 箭头符号 `->` 
> - 函数体
>
> ```java
> (int x, int y) -> {return x + y;}
> ```
>
>
>
> 函数体既可以是一个表达式，也可以是一个语句块：
>
> - 表达式：表达式会被执行然后返回执行结果。
> - 语句块：语句块中的语句会被依次执行，就像方法中的语句一样
>   - `return` 语句会把控制权交给匿名方法的调用者
>   - `break` 和 `continue` 只能在循环中使用
>   - 如果函数体有返回值，那么函数体内部的每一条路径都必须返回值



> Lambda表达式更多详细内容可以参阅以下文章
>
> - [Java 8 Lambda表达式](https://www.cnblogs.com/IcanFixIt/p/6744973.html)
> - [深入理解Java 8 Lambda（语言篇——lambda，方法引用，目标类型和默认方法）](http://www.cnblogs.com/figure9/archive/2014/10/24/4048421.html)
> - [函数式编程和命令式编程](https://blog.csdn.net/bruno231/article/details/6425710)

  

## 3. 方法与构造函数引用

参考 [深入理解Java 8 Lambda（语言篇——lambda，方法引用，目标类型和默认方法）](http://www.cnblogs.com/figure9/archive/2014/10/24/4048421.html)

  

## 4. 接口的默认方法

参考[深入理解Java 8 Lambda（语言篇——lambda，方法引用，目标类型和默认方法）](http://www.cnblogs.com/figure9/archive/2014/10/24/4048421.html)

  

## 5. Stream

流`Stream`是 Java SE 8 类库中新增的关键抽象，它被定义于 `java.util.stream`。

每个流代表一个值序列，流提供一系列常用的聚合操作，使得我们可以便捷的在它上面进行各种运算。

集合类库也提供了便捷的方式使我们可以以操作流的方式使用集合、数组以及其它数据结构。

流的操作可以被组合成 **流水线**（Pipeline）。

> - Java 8.x 引入的 `Stream`与之前 I/O 中描述的输入输出流`InputStream ` `OutputStream` 毫无关系。
>
> - Java 8.x中定义的 `Stream` 的概念是：
>   - `Stream` 是一个单向、不可往复、只能遍历一次的数据流
>   - `Stream` 不是集合元素，它不是数据结构并不保存数据



>  Stream更多详细内容可以参考以下文章：
>
>  - [Java 8 Stream](http://note.youdao.com/noteshare?id=46e37014ade32ef6fb1e5a40520614fe)
>  - [Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)
>  - [深入理解Java 8 Lambda（类库篇——Streams API，Collector和并行）](https://www.tuicool.com/articles/umiMjmE)
>  - [Java8 Stream原理深度解析](https://www.cnblogs.com/Dorae/p/7779246.html)



## 6. 全新的时间日期API

Java 8.x 中引入了一套全新的日期时间API（位于`java.time`包中）。

新的API最大优点在于，清楚定义了日期时间相关的一些概念。

- Date（日期）
- Time（时间）
- Time-Zone（时区）
- Instant（瞬时时间）
- Duration（持续时间）
- Period（时间段）

Java 8.x仍然延用了ISO的日历体系，并且与它的前辈们不同，`java.time` 包中的类是不可变且线程安全的。



>  更多新时间日期API的详细内容可参考
>
>  - [Java 8 中新的日期时间库](http://note.youdao.com/noteshare?id=b7898eb667ee333199cc946223e6fd89)
>  - [Java 8 Date-Time API 详解](https://www.cnblogs.com/IcanFixIt/p/8539007.html)

   

### TimeZones

在新API中时区使用`ZoneId`来表示。

时区可以很方便的使用静态方法`of()`来获取到。

时区定义了到UTS时间的时间差，在Instant时间点对象到本地日期对象之间转换的时候是极其重要的。

```java
System.out.println(ZoneId.getAvailableZoneIds());
// prints all available timezone ids
ZoneId zone1 = ZoneId.of("Europe/Berlin");
ZoneId zone2 = ZoneId.of("Brazil/East");
System.out.println(zone1.getRules());
System.out.println(zone2.getRules());
// ZoneRules[currentStandardOffset=+01:00]
// ZoneRules[currentStandardOffset=-03:00]
```

   

### java.time.Clock

Clock类提供了访问当前日期和时间的方法。

Clock是时区敏感的，可以用来取代 `System.currentTimeMillis()` 来获取当前的微秒数。

某一个特定的时间点也可以使用`Instant`类来表示，`Instant`类也可以用来创建老的`java.util.Date`对象。

```java
Clock clock = Clock.systemDefaultZone();
long millis = clock.millis();

Instant instant = clock.instant();
Date legacyDate = Date.from(instant);   // legacy java.util.Date
```



### java.time.Instant

`Instant`实例表示时间线上的一个点。

>  参考点是标准的Java纪元(epoch)，即1970-01-01T00：00：00Z（1970年1月1日00:00 GMT）。

```java
Instant now = Instant.now();  // 返回一个表示当前时间的Instant对象
now.getEpochSecond();         // 返回自纪元以来经过的秒数

```



### java.time.LocalTime

`LocalTime` 定义了一个没有时区信息的时间。（本地时间）

```java
LocalTime now1 = LocalTime.now(zone1);
LocalTime now2 = LocalTime.now(zone2);
System.out.println(now1.isBefore(now2));  // false

long hoursBetween = ChronoUnit.HOURS.between(now1, now2);
long minutesBetween = ChronoUnit.MINUTES.between(now1, now2);

System.out.println(hoursBetween);       // -3
System.out.println(minutesBetween);     // -239
```

`LocalTime` 提供了多种工厂方法来简化对象的创建，包括解析时间字符串。

```java
LocalTime late = LocalTime.of(23, 59, 59);
System.out.println(late);       // 23:59:59

DateTimeFormatter germanFormatter = DateTimeFormatter
        .ofLocalizedTime(FormatStyle.SHORT)
        .withLocale(Locale.GERMAN);

LocalTime leetTime = LocalTime.parse("13:37", germanFormatter);
System.out.println(leetTime);   // 13:37
```

   

### java.time.LocalDate

`LocalDate` 表示了一个确切的日期，比如 *2014-03-11*。

该对象值是不可变的，操作返回的总是一个新实例，用起来和`LocalTime`基本一致。

```java
LocalDate today = LocalDate.now();
LocalDate tomorrow = today.plus(1, ChronoUnit.DAYS);
LocalDate yesterday = tomorrow.minusDays(2);
LocalDate independenceDay = LocalDate.of(2014, Month.JULY, 4);
DayOfWeek dayOfWeek = independenceDay.getDayOfWeek();

System.out.println(dayOfWeek);    // FRIDAY
```

从字符串解析一个`LocalDate`类型和解析`LocalTime`一样简单：

```java
DateTimeFormatter germanFormatter = DateTimeFormatter
        .ofLocalizedDate(FormatStyle.MEDIUM)
        .withLocale(Locale.GERMAN);

LocalDate xmas = LocalDate.parse("24.12.2014", germanFormatter);
System.out.println(xmas);   // 2014-12-24
```

   

### java.time.LocalDateTime

`LocalDateTime` 同时表示了时间和日期，相当于前两节内容合并到一个对象上了。

`LocalDateTime`和`LocalTime`还有`LocalDate`一样，都是不可变的。

`LocalDateTime`提供了一些能访问具体字段的方法。

```java
LocalDateTime sylvester = LocalDateTime.of(2014, Month.DECEMBER, 31, 23, 59, 59);
DayOfWeek dayOfWeek = sylvester.getDayOfWeek();
System.out.println(dayOfWeek);      // WEDNESDAY

Month month = sylvester.getMonth();
System.out.println(month);          // DECEMBER

long minuteOfDay = sylvester.getLong(ChronoField.MINUTE_OF_DAY);
System.out.println(minuteOfDay);    // 1439
```

只要附加上时区信息，就可以将其转换为一个时间点`Instant`对象，`Instant`时间点对象可以很容易的转换为老式的`java.util.Date`。

```java
Instant instant = sylvester
        .atZone(ZoneId.systemDefault())
        .toInstant();

Date legacyDate = Date.from(instant);
System.out.println(legacyDate);     // Wed Dec 31 23:59:59 CET 2014
```

格式化`LocalDateTime`和格式化时间和日期一样的，除了使用预定义好的格式外，也可以自己定义格式：

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MMM dd, yyyy - HH:mm");

LocalDateTime parsed = LocalDateTime.parse("Nov 03, 2014 - 07:13", formatter);
String string = formatter.format(parsed);
System.out.println(string);     // Nov 03, 2014 - 07:13
```

    

## 7. Optional

到目前为止，臭名昭著的空指针异常是导致Java应用程序失败的最常见原因。

> 以前，为了解决空指针异常，Google公司著名的Guava项目引入了`Optional`类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。受到Google Guava的启发，`Optional`类已经成为Java 8类库的一部分。

`Optional`实际上是个容器：它可以保存类型T的值，或者仅仅保存`null`。

`Optional`提供很多有用的方法，这样我们就不用显式进行空值检测。

```java
String name = null;

// 传入一个name
Optional<String> fullName = Optional.ofNullable(name);

// 如果Optional类的实例为非空值的话，isPresent()返回true，否从返回false
System.out.println("Full Name is null? " + fullName.isPresent());

// 为了防止Optional为空值，orElseGet()方法通过回调函数来产生一个默认值
System.out.println("Full Name: " + fullName.orElseGet(() -> "[none]"));

// map()函数对当前Optional的值进行转化，然后返回一个新的Optional实例
// orElse()方法和orElseGet()方法类似，但是orElse接受一个默认值而不是一个回调函数
System.out.println(fullName.map(s -> "Hey " + s + "!").orElse("Hey Stranger!"));

// ifPresent()函数只在值不为空的情况下执行, 避免了null值造成的影响
fullName.ifPresent(n -> System.out.println("Hey " + n + "!"));
```

     


## 8. 多重注解

Java 8.x 中支持了多重注解，它允许把同一个类型的注解使用多次，只需要给该注解标注一下`@Repeatable`即可

```java
// Java 8.x 以前, 不支持多重注解的使用方式
@Hints({
    @Hint("hint1"),
    @Hint("hint2")
})
class Person {}


// Java 8.x 支持多重注解后的使用方式
@Hint("hint1")
@Hint("hint2")
class Person {}
```



## 9. Base64

在Java 8中，`Base64`编码已经成为Java类库的标准。

```java
final String text = "Base64 finally in Java 8!";

final String encoded = Base64.getEncoder().encodeToString(text.getBytes(StandardCharsets.UTF_8));
System.out.println(encoded);  // QmFzZTY0IGZpbmFsbHkgaW4gSmF2YSA4IQ==
final String decoded = new String(Base64.getDecoder().decode(encoded), StandardCharsets.UTF_8);
System.out.println(decoded);  // Base64 finally in Java 8!
```

Base64类同时还提供了对URL、MIME友好的编码器与解码器：

- `Base64.getUrlEncoder()`
- `Base64.getUrlDecoder()`
- `Base64.getMimeEncoder()`
- `Base64.getMimeDecoder()`

    

## 10. JVM新特性

- `PermGen`空间被移除了，取而代之的是`Metaspace`（JEP 122）
- JVM选项`-XX:PermSize`与`-XX:MaxPermSize`分别被`-XX:MetaSpaceSize`与`-XX:MaxMetaspaceSize`代替

    