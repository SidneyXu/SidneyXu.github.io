---
comments: true
date: 2015-12-08T10:44:06+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-50-lazy"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 50.Lazy
toc: true
topics:
- JGSK
---

## 共通篇

Lazy变量也就是常说的惰性加载，即变量在初始化时没有进行计算操作，而是延迟到了该变量第一次被使用的时候。在函数式编程中，惰性加载被广泛使用，尤其是对于一些长度未知的列表，如果使用普通方式，恐怕加载时系统就会被拖慢速度甚至于内存溢出。

<!--more-->

## Java

Java 并不支持惰性加载，所以以往一般都是通过代理对象的形式来模拟实习。不过从 Java 1.8 开始可以使用更简单的 Lambda 表达式来实现此功能。

```java
class A {
    private Supplier<Long> fooField = () -> {
        Long val = expensiveInit();
        fooField = () -> val;
        return val;
    };

    protected Long expensiveInit() {
        System.out.println("Compute...");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return System.currentTimeMillis();
    }

    public Long getFoo() {
        return fooField.get();
    }
}
```

调用以下代码观察控制台输出就可以看出区别

```java
System.out.println("Before is " + System.currentTimeMillis());
A a = new A();
System.out.println("After is " + System.currentTimeMillis());
System.out.println("Hello 1, " + a.getFoo());
System.out.println("Hello 2, " + a.getFoo());
```

## Groovy

Groovy 中可以通过 `@Lazy`  注解和自释放的闭包来实现惰性加载。

```groovy
class A {
    def fooField = {
        println("Compute...")
        Thread.sleep(3000)
        System.currentTimeMillis()
    }()

    @Lazy
    def lazyField = {
        println("Compute...")
        Thread.sleep(3000)
        System.currentTimeMillis()
    }()
}
```

调用以下代码观察控制台输出就可以看出区别

```groovy
println("Before is ${System.currentTimeMillis()}")
def a = new A()
println("After is ${System.currentTimeMillis()}")
println("Hello 1, ${a.fooField}")
println("Hello 2, ${a.fooField}")

println("Hello 1, ${a.lazyField}")
println("Hello 2, ${a.lazyField}")
```

## Scala

Scala 中通过 `lazy` 关键字来实现惰性加载的功能。

```scala
class A {
  val fooField = {
    println("Compute...")
    Thread.sleep(3000)
    System.currentTimeMillis()
  }

  lazy val lazyField = {
    println("Compute...")
    Thread.sleep(3000)
    System.currentTimeMillis()
  }
}
```

调用以下代码观察控制台输出就可以看出区别

```scala
println(s"Before is ${System.currentTimeMillis()}")
val a = new A()
println(s"After is ${System.currentTimeMillis()}")
println(s"Hello 1, ${a.fooField}")
println(s"Hello 2, ${a.fooField}")

println(s"Hello 1, ${a.lazyField}")
println(s"Hello 2, ${a.lazyField}")
```

## Kotlin

Kotlin 中通过 `lazy()` 函数来实现惰性加载功能。

```kotlin
class A {
    val fooField: Long by lazy {
        println("Compute...")
        Thread.sleep(3000)
        System.currentTimeMillis()
    }
}
```

调用以下代码观察控制台输出就可以看出区别

```kotlin
println("Before is ${System.currentTimeMillis()}")
val a = A()
println("After is ${System.currentTimeMillis()}")
println("Hello 1, ${a.fooField}")
println("Hello 2, ${a.fooField}")
```

Kotlin 中使用惰性加载时还能指定线程安全的模式，默认惰性加载是同步的（LazyThreadSafetyMode.SYNCHRONIZED，只能有一个线程进行修改，结果对所有线程可见），但是也可以指定为公开的(PUBLICATION，所有线程可以同时进行计算，但是只有第一个完成计算的线程会被作为结果) ， 或者不加任何限制(NONE，没有任何线程安全的措施，效率最高，适合本来就是单线程执行计算的程序)。

```kotlin
val fooField2: Long by lazy(LazyThreadSafetyMode.PUBLICATION) {
    println("Compute...")
    Thread.sleep(3000)
    System.currentTimeMillis()
}
```

## 总结

- 除了 Java 之外，其它三种语言都默认支持惰性加载。


---

项目源码见 [JGSK/_34_lazy](https://github.com/SidneyXu/JGSK)
