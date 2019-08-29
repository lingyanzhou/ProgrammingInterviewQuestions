# Java Basics

## Java IO (java.io包) 流

![Java IO Streams](img/Java-IO-streams.png)

[Java总结：Java 流(Stream)、文件(File)和IO](https://www.cnblogs.com/52fhy/p/8232825.html)

- Apache Common IO: IOUtils 常用操作

```java
IOUtils::copy(inputstream,outputstream)
IOUtils::copy(inputstream,writer)
IOUtils::copy(inputstream,writer,encoding)
IOUtils::copy(reader,outputstream)
IOUtils::copy(reader,writer)
IOUtils::copy(reader,writer,encoding)

IOUtils::copyLarge(reader,writer) 默认会用1024*4的buffer来读取
IOUtils::copyLarge(reader,writer,buffer)

IOUtils.toBufferedInputStream(inputstream)
```

## Java BIO, NIO, AIO

一个I/O操作其实分成了两个步骤：发起I/O请求和实际的I/O操作。

同步I/O和异步I/O的区别就在于第二个步骤是否阻塞，如果实际的I/O读写阻塞请求进程，那么就是同步I/O。

阻塞I/O和非阻塞I/O的区别在于第一步，发起I/O请求是否会被阻塞，如果阻塞直到I/O完成，那么就是传统的阻塞I/O，如果不阻塞，那么就是非阻塞。

- BIO (BLocking IO)

由java.io包提供, 同步阻塞。

- NIO (Non-blocking IO)

由java.nio包提供, 同步非阻塞。使用Selector多路复用。

- AIO (Async-IO)

由java.nio包提供, 异步非阻塞。

[Java核心（五）深入理解BIO、NIO、AIO](https://my.oschina.net/u/3471412/blog/2966696)

## 反射 (Reflection)

- 定义

通过采用某种机制来实现对自己行为的描述（self-representation）和监测（examination），并能根据自身行为的状态和结果，调整或修改应用所描述行为的状态和相关的语义。

- 实例

Reflection allows inspection of classes, interfaces, fields and methods at runtime without knowing the names of the interfaces, fields, methods at compile time. It also allows instantiation of new objects and invocation of methods.

  - Discover and modify source-code constructions (such as code blocks, classes, methods, protocols, etc.) as first-class objects at runtime.

  - Convert a string matching the symbolic name of a class or function into a reference to or invocation of that class or function.

  - Evaluate a string as if it were a source-code statement at runtime.

  - Create a new interpreter for the language's bytecode to give a new meaning or purpose for a programming construct.


- Java中的反射:

载入指定的类, 获取构造函数, 字段和方法

```java
public class StringBuf {
    public String str;
    public String toUpper() {
        return str.toUpperCase();
    }
}
```

```java
// 通过类名获取类
Class clazz = Class.forName("lzhou.learning.concurrency.concurrency.StringBuf";
	
// 获取所有注释
Annotation[] annotations = clazz.getAnnotations();
Assert.assertEquals(1, annotations.length);
// 获取注释
Annotation deprecatedAnnotation =clazz.getAnnotation(Deprecated.class);
Assert.assertNotNull(deprecatedAnnotation);
	
// 通过参数列表获取公有构造函数
StringBuf obj = (StringBuf) clazz.getConstructor(String.class).newInstance("Test");
	
// 通过方法名名称和方法参数, 获取公有方法
String val = (String) clazz.getMethod("toUpperCase",new Class<?>[0]).invoke(obj);
Assert.assertEquals("TEST", val);
	
// 通过名称获取公有字段
val = (String) clazz.getField("str").get(obj);
Assert.assertEquals("Test", val);
```

## JVM类加载过程

![JVM类加载过程](img/JVM-class-loading-details.png)

[JVM Architecture – Understanding JVM Internals](https://www.javainterviewpoint.com/java-virtual-machine-architecture-in-java/)

## JVM引用类型

|   类型   | 回收时间 |            使用场景    |
|----------|---------|-----------------------|
|  强引用  | 一直存活，除非GC Roots不可达 | 所有程序的场景，基本对象，自定义对象等。 |
|  软引用  | 内存不足时会被回收 | 一般用在对内存非常敏感的资源上，用作缓存的场景比较多 |
|  弱引用  | 只能存活到下一次GC前 | 生命周期很短的对象，例如ThreadLocal中的Key。 |
|  虚引用  | 随时会被回收， 创建了可能很快就会被回收 |  业界暂无使用场景， 可能被JVM团队内部用来跟踪JVM的垃圾回收活动 |

[Java中的四种引用类型（强、软、弱、虚）](https://www.jianshu.com/p/ca6cbc246d20)

## Java类型擦除 (Type Erasure)

Java的泛型只存在于编译时期，泛型使编译器可以在编译期间对类型进行检查以提高类型安全，减少运行时由于对象类型不匹配引发的异常。

- 类型擦除和类型具体化(Type Erasure and Type Reified)

  - 类型擦除
  
  泛型类型仅存在于编译期间，编译后的字节码和运行时不包含泛型信息。 例如Java。 

  - 类型具体化

  泛型类型存在于编译和运行期间，编译器自动为每一种泛型类型生成类型代码并编译进二进制码中。例如C++。 

- 类型擦除的局限性

  - 运行时隐含类型转换的开销

  - 类型参数不能实例化

  - 类型参数不能进行类型查询(类型查询在运行时，运行时类型参数已被擦除)

  - 不能在静态域和静态方法中引用类型变量

  - 重载方法签名冲突

  - 一个类不能实现同一个泛型接口的两种变体

  - 泛型类不能扩展java.lang.Throwable

[Java泛型: 类型擦除(type erasure)](https://segmentfault.com/a/1190000008711188)

## Java类型不变协变,逆变 (Type Invariant, Covariant, Contravariant)

定义:

如果A、B表示类型，f(⋅)表示类型转换，≤表示继承关系（比如，A≤B表示A是由B派生出来的子类）；


- f(⋅)是逆变（contravariant）的，当A≤B时有f(B)≤f(A)成立；

- f(⋅)是协变（covariant）的，当A≤B时有f(A)≤f(B)成立；

- f(⋅)是不变（invariant）的，当A≤B时上述两个式子均不成立，即f(A)与f(B)相互之间没有继承关系。

Java中泛型是不变的,而数组是协变的。

Integer是Number子类, 但是ArrayList<Integer>不是ArrayList<Number>子类。

```java
// 编译报错
// required ArrayList<Integer>, found ArrayList<Number>
ArrayList<Integer> list1 = new ArrayList<>();
ArrayList<Number> list2 = list1;
```

而Integer[]是Number[]子类。

```java
// 可以正常通过编译,正常使用
Integer[] arr1 = new Integer[]{1, 2};
Number[] arr2 = arr1;
```

[java泛型 通配符详解及实践](https://www.jianshu.com/p/e3d58360e51f)

## Java泛型通配符 (Generics Wildcard)

Java的泛型没有协变性和逆变性, 泛型通配符使Java泛型获得协变和逆变相似的效果。

- 无界限通配符

```java
List<?>
```

与`<? extends Object>`等效。

- 上界限通配符

```java
List<? extends 父类型>
```

使容器具有协变性, 即List<子类型>是List<父类型>的子类型。

因为可以确定父类型，所以可以以父类型去获取数据（向上转型）。但是不能写入数据。

Java泛型的类型信息已经被擦除为Object，无法做类型检测，只能依靠声明在编译时做严格的类型检查，上界限通配符声明意味着这个容器中的元素类型不确定，可能是任何子类，所以往里面添加任何类型都是不安全的。

例:

```java
List<? extends Fruit> a = new ArrayList<Apple>();
//a.add(new MochaCoffee()); //不能添加MochaCoffee
//a.add(new Coffee()); //也不能添加Coffee
a.add(null);
Fruit fruit = a.get(0);
```

- 下界限通配符

```java
List<? super 子类型>
```

使容器具有逆变性, 即List<父类型>是List<子类型>的子类型。

因为可以确定最小类型，所以可以以最小类型去写入数据（向下转型）。而不能获取数据。

例:

```java
List<? super Apple> a = new ArrayList<Fruit>();
a.add(new Apple());
```

- PECS原则 (Producer Extends Consumer Super)

  - 上界`<? extends T>`不能往里存，只能往外取
  - 下界`<? super T>`不影响往里存，但往外取只能放在Object对象里


[泛型通配符上下界限的理解](https://blog.csdn.net/wang252949/article/details/80583093)