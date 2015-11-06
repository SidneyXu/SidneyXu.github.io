---
comments: true
date: 2015-11-06T14:26:07+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-30-thread-future"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 30.Thread 与 Future"
toc: true
topics:
- JGSK
---

## Java

### Thread

线程主要用于执行并发任务，提高 CPU 的运行效率。Java 中线程相关的概念主要有两个： Thread 和 Runnable。

<!--more-->

Runnable 是一个只含有 `run()` 方法的接口，该接口定义了线程所要执行的代码。Thread 表示线程，本身实现了 Runnable 接口，可以执行自己的 `run()` 方法也可以执行传入的 Runnable 接口的 `run()` 方法。

定义一个简单的线程

```java
new Thread(() -> {
    System.out.println("async");
}, "thread-01").start();
System.out.println("sync");
```

### Thread Pool

如果总是建立新的线程，CPU 资源很快会被耗尽，因此便有了线程池。线程池本质是预先建立好一些空闲线程放在池子中，当需要在线程中执行代码时便从池中取出空闲的线程来运行，运行完毕后线程继续放回池中，如果需要执行代码但是池中没有线程时根据不同的线程池的策略或者新建线程或者将请求放在等待队列中。

Java 中创建线程池非常简单，主要使用 `Executors` 这一个工具类。

创建一个单线程的线程池

```java
ExecutorService service = Executors.newSingleThreadExecutor();
service.execute(() -> System.out.println("Running in a pool."));
```

### Callable 与 Future

Callable 与 Future 主要用于从线程中获得返回结果。Callable 用于在线程中执行计算并返回结果，Future 用于等待线程的结果。

```java
ExecutorService service = Executors.newSingleThreadExecutor();
Callable<String> callable = () -> "Hello World";
Future<String> future = service.submit(callable);
String result = future.get();
System.out.println(result);
service.shutdown();
```

### Timer

Timer 用于执行定时任务，也可以用 ScheduledThreadPool 进行替代。

```java
Timer timer = new Timer("timer", true);
TimerTask task = new TimerTask() {
    @Override
    public void run() {
        System.out.println("ping!");
    }
};
timer.schedule(task, 100, 100);
Thread.sleep(1000);
timer.cancel();
```


线程还有很多复杂的概念，很难在一节中介绍清楚，所以本节只介绍基本的内容，其余的请看各种参考书。

## Groovy

与 Java 相同。

## Scala

### Thread

用法同 Java。

### ExecutionContext

ExecutionContext 可以看做 Java 中的线程池，实际上其本质也是通过 Java 的 `ExecutorService` 来实现的。

```scala
val executor = Executors.newFixedThreadPool(1)
implicit val service = ExecutionContext.fromExecutor(executor)
service.execute(new Runnable {
  override def run(): Unit = {
    println("Running in a pool.")
  }
})
```

除此之外，Scala 还有一个全局的默认线程池 `scala.concurrent.ExecutionContext.Implicits.global`，通常来说这个线程池仅用于测试。因为你不知道其它人或者你引用的其它库中是否有使用默认的线程池进行大量长时间操作或者 Block 操作。

### Future 与 Promise

#### Future

Future 的概念与 Java 相似，用于从线程中取出执行结果。

```scala
val s = "Hello"
val f1: Future[String] = Future {
  s + " future!"
}
f1 onSuccess {
  case msg =>
    println(msg, Thread.currentThread().getName)
    msg
}
f1 onComplete {
  case msg => println("onComplete")
}
val result = Await result(f1, Duration(3, TimeUnit.SECONDS))
println(result)
```

#### Promise

Promise 与 Future 不同，Future 提供了读取计算值的接口，而 Promise 提供了写入计算值的接口。


## Kotlin

### Thread

Kotlin 用法与 Java 相同，但是由于 Kotlin 对部分方法进行了包装，所以可以节省一定的代码量。

定义一个简单的线程

```kotlin
thread() {
    println("async")
}
println("sync")
```

### Thread Pool

创建一个单线程的线程池

```kotlin
val service = Executors.newSingleThreadExecutor()
service.execute {
    println("Running in a pool.")
}
```

### Callable 与 Future

```kotlin
val future = service.submit(
        Callable() {
            "Hello World"
        }
)
val result = future.get()
println(result)
service.shutdown()
```

### Timer

```kotlin
val fixedRateTimer = fixedRateTimer(name = "timer",
        daemon = true, initialDelay = 100, period = 100) {
    println("ping!")
}
Thread.sleep(1000)
fixedRateTimer.cancel()
```


## 总结

- 


---

项目源码见 [JGSK/_30_thread_future](https://github.com/SidneyXu/JGSK)
