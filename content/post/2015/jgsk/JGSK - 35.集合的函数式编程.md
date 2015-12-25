---
comments: true
date: 2015-12-25T11:48:59+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "jgsk-35-collection-function"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 35.集合的函数式编程"
toc: true
topics:
- JGSK
---

## Java

从 Java 1.8 开始 Java 也支持了集合的函数式编程。

### Stream

Stream 是 Java 1.8 引入的类，主要用于进行集合的流式操作，大部分流式操作都会返回一个新的流。

集合可以使用调用 `stream()` 方法或者使用 `Stream` 类的静态方法来转换为 `Stream` 对象。

```java
numbers1.stream();
Stream.of(numbers1);
```

### Map 操作

Map 操作用于对集合中的每一个输入元素应用指定的函数从而得到另一种输出。

```java
Stream<Integer> mapResult = numbers1.stream().map(x -> x * x);
System.out.println("map => " + getString(mapResult));   //  [1, 4, 9, 16, 25, 36]
```

### FlatMap 操作

FlatMap 操作用于对输入元素进行抽出，得到扁平化的输出。

```java
Stream<Integer> flatMapResult = numbers3.stream().flatMap(list -> list.stream().map(x -> x * 10));
System.out.println("flatMap => " + getString(flatMapResult));   //  [10, 20, 30, 40, 50, 60, 100, 200, 300]
```

### Reduce 操作

Reduce 操作用于对集合中的每一个元素进行指定的计算从而得到计算结果。计算的过程是将第一个元素和第二个元素进行计算，得到的结果和第三个元素再进行计算，以此类推。

```java
Optional<Integer> reduceResult = numbers1.stream().reduce((n1, n2) -> n1 - n2;
System.out.println("reduce => " + reduceResult.get());  //  -19
```

### Filter 操作

Filter 操作通过对集合中的每一个元素应用一个返回值是布尔值的函数来获得一个返回值都是 `true` 的新的集合.

```java
Stream<Integer> filterResult = numbers1.stream().filter(n -> n % 2 == 0);
System.out.println("filter => " + getString(filterResult)); //  [2, 4, 6]
```

### Collect 操作

Collect 操作接收偏函数并返回偏函数处理的分组的结果集。

```java
Map<Boolean, List<Integer>> collectResult = numbers1.stream().collect(Collectors.partitioningBy(x -> x % 2 == 0));
System.out.println("collect => " + collectResult);  //  {false=[1, 3, 5], true=[2, 4, 6]}
```

### ForEach 操作

ForEach 操作用于对集合进行遍历。

```java
numbers1.stream().forEach(System.out::println);
```

### Parallel 操作

Stream 操作默认都是单线程的。但是可以使用 ParallelStream 进行并发的流式操作，并发默认使用 `ForkJoinPool.commonPool`。ParallelStream 和 Stream 在使用上唯一区别就是使用 `parallelStream()` 替代 `stream()`。

```java
numbers1.parallelStream().forEach(System.out::println);
```

使用 ParallelStream 时集合中的某一个元素完成当前流的计算后就会立即进入下一个控制流，而不会像 Stream 一样只有集合的所有元素都完成操作后才会进行下一个控制流。

```java
Stream<Integer> parallelResult = numbers1.parallelStream().map(x -> {
    System.out.println("stream first: " + x + " on " + Thread.currentThread().getName());
    return x;
}).map(x -> {
    System.out.println("stream second: " + x + " on " + Thread.currentThread().getName());
    return x;
}).map(x -> {
    System.out.println("stream third: " + x + " on " + Thread.currentThread().getName());
    return x;
});
System.out.println(getString(parallelResult));
```


## Groovy




## 总结

- 

---

项目源码见 [JGSK/_35_collection_function](https://github.com/SidneyXu/JGSK)