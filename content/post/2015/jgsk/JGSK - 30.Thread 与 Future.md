n---
comments: true
date: 2015-11-06T14:26:07+08:00
description: ""
draft: false
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

线程主要用于执行并发任务，提高 CPU 的运行效率。在各种参考中都有线程相关的各种概念，在这里就补多废话了。在Java 中线程相关的概念主要有两个： Thread 和 Runnable。

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

### CompletableFuture

#### 概念

CompletableFuture 是 Java 1.8 新加入的类，提供了事件驱动的编程模型，能够大幅简化异步调用的繁琐过程从而避免陷入回调地狱。

CompletableFuture 继承自 Future，所以也表示未来的值，但是 CompletableFuture 表示的未来的值可以是预期的，即你可以明确表明你知道将会发生什么。

#### 创建 CompletableFuture

创建一个最简单的 CompletableFuture 的实例

```java
CompletableFuture<Integer> firstFuture = new CompletableFuture<>();
firstFuture.complete(10);
int value = firstFuture.get();
System.out.println(value);  //  10
```

以上调用 `complete()` 表示我知道这个 Future 会返回 10。这个例子是最简单的同步调用方式，下面使用工厂类创建一个支持异步调用的 CompletableFuture 实例。

```java
 ExecutorService service = Executors.newFixedThreadPool(10);
 CompletableFuture<Integer> secondFuture = CompletableFuture.supplyAsync(() -> {
      //  long running
      return new Random().nextInt(1000);
  }, service);
```

以上 `supplyAsync()` 创建了一个支持异步调用的 CompletableFuture，该 CompletableFuture 的任务运行在指定的线程池中。

#### 在同一个 CompletableFuture 上执行链式操作

CompletableFuture 的用法类似 Javascript 的 Promise 编程的概念。使用 `thenApplyAsync()` 或 `thenApply()` 可以在同一个 CompletableFuture 上可以执行多个链式操作，前一个操作完成后才能执行下一个操作。

```java
CompletableFuture<String> thirdFuture = secondFuture.thenApplyAsync(integer -> {
    if (integer < 900) {
        throw new IllegalArgumentException();
    }
    return "" + integer;
});
```

#### 完成 CompletableFuture

`thenAcceptAsync()` 或 `thenAccept()` 可以在一个 CompletableFuture 完成所有操作后执行收尾操作。

```java
CompletableFuture<Void> lastFuture = thirdFuture.thenAcceptAsync(s -> System.out.println("Result is " + s));
```

#### 异常处理

`exceptionally()` 方法会在 CompletableFuture 执行中发生异常时被调用，可以在该方法中处理异常。

```java
CompletableFuture<String> safe1 = thirdFuture.exceptionally(throwable -> {
        //  throwable is CompletionException
        if (throwable != null && (throwable.getCause() instanceof IllegalArgumentException)) {
            return "Too small.";
        } else if (throwable != null) {
            return throwable.getMessage();
        }
        return null;
    });
```

或者也可以使用 `handleAsync()` 方法，无论异常是否发生，该方法都会被调用，所以即可以在返回前处理异常也可以处理返回值。

```java
CompletableFuture<String> safe2 = thirdFuture.handleAsync((s, throwable) -> {
    //  throwable is CompletionException
    if (throwable != null && (throwable.getCause() instanceof IllegalArgumentException)) {
        return "Result is too small.";
    } else if (throwable != null) {
        return throwable.getMessage();
    }
    return s;
});
```


## Groovy

与 Java 相同，但是由于 Groovy 暂不支持 Java 1.8 的 Lamda 表达式，所以一些代码写起来会比 Java 麻烦。 

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

以上例子定义了一个容量为 1 的线程池，并且通过隐式转换（具体见 Scala 的隐式转换一章）使 ExecutionContext 运行在该线程池中。

除了自定义线程池之外，Scala 还有一个全局的默认线程池 `scala.concurrent.ExecutionContext.Implicits.global`，通常来说这个线程池仅用于测试。因为你不知道其它人或者你引用的其它库中是否有使用默认的线程池进行大量长时间操作。

### Future

Scala 中的 Future 表示尚未完成的计算结果，执行在指定的 ExecutionContext 中。一个 Future 一旦被赋值后就不可被改变，可以用于编写高效的并行代码。

#### 创建 Future

使用 `Future{}` 创建 Future 对象

```scala
val s = "Hello"
val f1: Future[String] = Future {
  s + " future!"
}
```

#### 回调

Future 支持三种回调方式

- `onSuccess` 用于在 Future 完成后对计算结果进行某些操作
- `onFailure` 用于处理在 Future 计算过程中发生的异常
- `onComplete` 用于在 Future 处理完成后进行某些操作，其接收的参数为 Try[T] 类型，子类为 Success[T] 和 Failure[T]，所以可以在其方法体内同时完成成功和失败的后续处理。

```scala
val s = "Hello"
//	使用 Future{} 创建 Future 对象
val f1: Future[String] = Future {
  s + " future!"
}
f1 onSuccess {
    case msg =>
        println(msg)
}
f1 onFailure {
    case e =>
        e.printStackTrace()
}
f1 onComplete {
    case Success(msg) => println(s"Success $msg")
    case Failure(e) => println(s"Failure ${e.getMessage}")
}
val result = Await result(f1, Duration(3, TimeUnit.SECONDS))
println(result)
```

一个 Future 上 可以定义多个 `onSuccess` 等类型的回调方法，但是所有的回调方法返回值都是 `Unit` 所以无法进行链式操作，同时执行多个回调的顺序的也不保证。

```java
val f2 = Future {
    Thread.sleep(10)
}
f2 onSuccess {
    case _ => println("a")
}
f2 onSuccess {
    case _ => println("b")
}
f2 onSuccess {
    case _ => println("c")
}
```

#### 组合

Callback 虽然方便，但是在复杂场景下光使用 Callback 很容易陷入回调地狱。

最简单的回调地狱

```scala
Future {
    1
} onSuccess {
    case x => Future {
        x + 3
    } onSuccess {
        case x => Future {
            x * 10
        } onSuccess {
            case x =>
                println(s"Result of callback hell is $x") //  40
        }
    }
}
```

为此 Scala 为 Future 提供了多种组合方式。

**map**

`map` 用于在 Future 执行完毕后产生新的 Future 并执行后续操作，无论异常是否会发生，Future 的结果都会进行传递。

```scala
val f1 = Future {
    1	//	or 1/0
}
val f2 = f1 map { x =>
    x + 3
}
f2 map { x =>
    x * 10
} onComplete {
    case Success(x) => println(s"Result of map is $x") //  40
    case Failure(e) => e.printStackTrace()
}
```

**Recovery**

`recover` 用于在 Future 执行过程中发生异常时进行一些拯救工作。

```scala
Future {
    1 / 0
} recover {
    case e: ArithmeticException => 0
} map { x =>
    x + 3
} map { x =>
    x * 10
} onComplete {
    case Success(x) => println(s"Result of recover is $x") //  30
    case Failure(e) => e.printStackTrace()
}
```

**Fallback**

`fallback` 用于在 Future 执行过程中发生异常时执行另一个 Future。如果异常没有发生，原始 Future 的结果会被返回。如果原始 Future 发生异常，新 Future 成功执行则返回新 Future 的结果，如果新 Future 中也发生异常，则原始 Future 的异常会被返回。

```scala
val f3 = Future {
    1 / 0
}
val f4 = Future {
    10
}
f3 fallbackTo f4 map { x =>
    x + 3
} onComplete {
    case Success(x) => println(s"Result of fallback is $x") //  13
    case Failure(e) => e.printStackTrace()
}
```

**Then**

`then` 听起来容易让人混淆，以为是连续多个 Future，前一个执行完的结果会被传递到后一个。但是实际 `then` 是为了解决副作用的问题，即 `then` 连接的多个 Furtue 总是返回初始 Future 的结果。

```scala
Future {
    1
} andThen {
    case Success(x) =>
        x + 1
} andThen {
    case Success(x) =>
        x + 10
} andThen {
    case Success(x) =>
        println(s"Result of then is $x") //  1
}
```

由以上例子可知 `x` 的值并没有发生改变。

### Promise

Promise 与 Future 不同，Future 只提供了读取计算值的接口，而 Promise 提供了写入计算值的接口，Java 1.8 提供的 CompletableFuture 就与 Promise 有些相似。

```scala

```


## Kotlin

Kotlin 基本就是直接调用 Java 代码，但是由于 Kotlin 对部分方法进行了包装，所以可以节省一定的代码量。

### Thread

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

### CompletableFuture

```kotlin
val firstFuture = CompletableFuture<Int>()
firstFuture.complete(10)
println(firstFuture.get())

service = Executors.newFixedThreadPool(10)
val secondFuture = CompletableFuture.supplyAsync(Supplier {
    Random().nextInt(1000)
}, service)
println(secondFuture.get())
service.shutdown()
```

## 总结

- 不同语言的内部实现都是依赖 Java API，所以非常相似
- Scala 的 Future 和 Promise 和 Java 1.8 引入的 CompletableFuture 可以更好的写出异步代码


---

项目源码见 [JGSK/_30_thread_future](https://github.com/SidneyXu/JGSK)
