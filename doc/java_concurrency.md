# Java并发

## 并发性质

- 原子性

一个操作或者多个操作，要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。

- 有序性

即程序执行的顺序按照代码的先后顺序执行。

- 可见性

可见性是指当一个线程修改了共享变量后，其他线程能够立即得知这个修改。

注: volatile关键字保证有序性和可见性, 但不保证原子性。 Synchronized关键字保证有序性, 可见性和保证原子性。

## JVM线程状态

![JVM thread state](img/jvm-thread-state.png)


## JVM结束线程的方法

- 线程内return 

- 线程内抛出异常

- 调用Thread.stop() (已过时)

  强制终止线程, 不保证线程逻辑完整, 和synchronized代码块里的原子性。

- 退出JVM

[[改善Java代码]不使用stop方法停止线程](https://www.cnblogs.com/DreamDrive/p/5623804.html)

## Thread.interrupt()

- 本线程中断自己是被允许的；其它线程调用本线程的interrupt()方法时，会通过checkAccess()检查权限。这有可能抛出SecurityException异常。
- 如果本线程是处于阻塞状态：调用线程的wait(), wait(long)或wait(long, int)会让它进入等待(阻塞)状态，或者调用线程的join(), join(long), join(long, int), sleep(long), sleep(long, int)也会让它进入阻塞状态。若线程在阻塞状态时，调用了它的interrupt()方法，那么它的“中断状态”会被清除并且会收到一个InterruptedException异常。
- 如果线程被阻塞在一个Selector选择器中，那么通过interrupt()中断它时；线程的中断标记会被设置为true，并且它会立即从选择操作中返回。
- 如果不属于前面所说的情况，那么通过interrupt()中断线程时，它的中断标记会被设置为“true”。
- 中断一个“已终止的线程”不会产生任何操作。

## 使用interrupt()优雅地中断线程

- 终止处于“阻塞状态”的线程

当线程由于被调用了sleep(), wait(), join()等方法而进入阻塞状态；若此时调用线程的interrupt()将线程的中断标记设为true。由于处于阻塞状态，中断标记会被清除，同时产生一个InterruptedException异常

```java
public void run() {
    try {
        while (true) {
            // 执行任务...
        }
    } catch (InterruptedException ie) {  
        // 由于产生InterruptedException异常，退出while(true)循环，线程终止！
    }
}
```

- 终止处于“运行状态”的线程

  - 通过“中断标记”终止线程

```java
@Override
public void run() {
    while (!isInterrupted()) {
        // 执行任务...
    }
}
```

  - 通过“额外添加标记”终止线程

```java
private volatile boolean flag = true;
public void stopThread() {
    flag = false;
}

@Override
public void run() {
    while (flag) {
        // 执行任务...
    }
}
```

  - 综合线程处于“阻塞状态”和“运行状态”的终止方式

```java
@Override
public void run() {
    try {
        // 1. isInterrupted()保证，只要中断标记为true就终止线程。
        while (!isInterrupted()) {
            // 执行任务...
        }
    } catch (InterruptedException ie) {  
        // 2. InterruptedException异常保证，当InterruptedException异常产生时，线程被终止。
    }
}
```

[Java多线程系列--“基础篇”09之 interrupt()和线程终止方式](https://www.cnblogs.com/skywang12345/p/3479949.html)

## ThreadLocal

- 原理

每个Thread内部维护着一个ThreadLocalMap，它是一个Map。这个映射表的Key是一个弱引用，其实就是ThreadLocal本身，Value是真正存的线程变量Object。

![ThreadLocal 原理](img/thread-local-detail.jpg)

- 基本操作
 
  - `ThreadLocal.withInitial()`: 设置缺省值

  - `ThreadLocal.set(T value)`

  - `ThreadLocal.get()`

  - `ThreadLocal.remove()`: 删除ThreadLocalMap中的Entry

- 应用

  - 方便传参

	- 适用于线程由框架管理时

    ```java
	ThreadLocal<Integer> threadId = ThreadLocal.withInitial(()->curId.getAndIncrement());
    
    Runnable printDate = () -> {
            System.out.println(threadId.get());
        };
    ```

  - 线程私有的对象
	- 适用于该对象不需要在多线程之间共享；该对象需要在线程内被传递; 该对象的方法线程不安全时。
	- 典型场景: 数据库连接管理，线程会话管理等场景。

    ```java
	private static final ThreadLocal<Integer> simpleDateTimeFormat = ThreadLocal.withInitial(()->  new SimpleDateFormat());
    
    Runnable printDate = () -> {
            System.out.println(simpleDateTimeFormat.get().format(new Date()));
        };
    ```

- 内存泄漏

由于ThreadLocalMap的key是弱引用，而Value是强引用。ThreadLocal在没有外部对象强引用时，发生GC时弱引用Key会被回收，而Value不会回收. 这些null key就存在一条强引用链的关系一直存在：Thread --> ThreadLocalMap-->Entry-->Value。这条强引用链会导致Entry不会回收，Value也不会回收，但Entry中的Key却已经被回收的情况，造成内存泄漏。

- key 使用弱引用的原因

key 使用弱引用：引用的ThreadLocal的对象被回收了，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。value在下一次ThreadLocalMap调用set,get，remove的时候会被清除。

- 内存泄漏的根源

由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key的value就会导致内存泄漏，而不是因为弱引用。


[ThreadLocal-面试必问深度解析](https://www.jianshu.com/p/98b68c97df9b)

[ThreadLocal内存泄漏真因探究](https://www.jianshu.com/p/a1cd61fa22da)