---
comments: true
date: 2015-12-25T11:48:59+08:00
description: ""
draft: false
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

Stream 是 Java 1.8 引入的类，主要用于进行 并行集合的流式操作。

集合可以使用调用 `stream()` 方法或者使用 `Stream` 类的静态方法来转换为 `Stream` 对象。

<!--more-->

```java
List<Integer> numbers1 = Arrays.asList(1, 2, 3, 4, 5, 6);
List<Integer> numbers2 = Arrays.asList(10, 20, 30);
List<List<Integer>> numbers3 = Arrays.asList(numbers1, numbers2);

numbers1.stream();
Stream.of(numbers1);
```

### Map 操作

Map 操作用于对集合中的每一个输入元素应用指定的函数从而得到另一种输出。输入类型和输出类型可以不一致。

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

Groovy 本身只提供了少量的集合的函数式编程的 API，但是由于可以调用 Java 代码所以也可以使用 Stream，不过目前 Groovy 对 Java 1.8 的支持并不好，所以写出的代码会非常难看。

### Stream 操作

Groovy 由于目前不支持 Java 1.8 的 Lambda 表达式，所以使用 Stream 时会非常繁琐。

```groovy
def numbers1 = [1, 2, 3, 4, 5, 6]
def mapResult = numbers1.stream().map(new Function<Integer, Integer>() {
    @Override
    Integer apply(Integer x) {
        x * x
    }
})
```

### ForEach 操作

ForEach 操作用于对集合进行遍历。

```groovy
numbers1.each { println(it) }
```

## Scala

### Map 操作

Map 操作用于对集合中的每一个输入元素应用指定的函数从而得到另一种输出。输入类型和输出类型可以不一致。


```scala
val numbers1 = List(1, 2, 3, 4, 5, 6)	
val numbers2 = List(10, 20, 30)
val numbers3 = List(numbers1, numbers2)
val mapResult: List[Int] = numbers1.map(x => x * x)
println(s"map => $mapResult") //  List(1, 4, 9, 16, 25, 36)
```

### FlatMap 操作

FlatMap 操作用于对输入元素进行抽出，得到扁平化的输出。

```scala
val flatMapResult: List[Int] = numbers3.flatMap(list => list.map(x => x * 10))
println(s"flatMap => $flatMapResult") //  List(10, 20, 30, 40, 50, 60, 100, 200, 300)       
```

如果只是想简单的抽出的话，可以直接使用更简单的 `flatten()` 方法。

```scala
println(s"flatten => ${numbers3.flatten}") //  List(1, 2, 3, 4, 5, 6, 10, 20, 30)
```

### Zip 操作

Zip 操作用于连接两个集合，新的集合以最短的集合为准。

```scala
val zipResult1: List[(Int, Int)] = numbers1.zip(numbers2)
println(s"zip1 => $zipResult1") //  List((1,10), (2,20), (3,30))

val zipResult2: List[(Int, Int)] = numbers2.zip(numbers1)
println(s"zip2 => $zipResult2") //  List((10,1), (20,2), (30,3))
```

除了 `zip()` 方法外还有一个 `zipAll()` 操作，该方法创建的集合以最长的集合为准，并且用指定值填充较短的集合。

```scala
val zipResult3: List[(Int, Any)] = numbers1.zipAll(numbers2, -1, 'a')
println(s"zipAll => $zipResult3") //  List((1,10), (2,20), (3,30), (4,a), (5,a), (6,a))
```

以上例子中当 `numbers1` 较短时，新集合中就会以 `-1` 填充不满足长度的位置，如果 `numbers2` 较短，则以 `a` 填充。

### Reduce 操作

Reduce 操作用于对集合中的每一个元素进行指定的计算从而得到计算结果。计算的过程是将第一个元素和第二个元素进行计算，得到的结果和第三个元素再进行计算，以此类推。

Scala 有三个版本的 Reduce 操作，默认的版本第一个元素是集合的最左还是最右是不确定的，由 Scala 编译器自己决定。

```scala
val reduceResult = numbers1.reduce((n1, n2) => n1 - n2)
println(s"reduce => $reduceResult") //  -19
```

也可以通过 `reduceLeff()` 指定从左开始为集合的第一个元素。

```scala
val reduceLeftResult = numbers1.reduceLeft((n1, n2) => n1 - n2)
println(s"reduceLeft => $reduceLeftResult") //  -19
```

同样还有 `reduceRight()` 的版本

```scala
val reduceRightResult = numbers1.reduceRight((n1, n2) => n1 - n2)
println(s"reduceRight => $reduceRightResult") //  -3
```

### Fold 操作

Fold 操作是给定一个初始值，然后集合中的第一个元素与初始值进行计算，计算结果再与集合中的下一个元素进行计算，以此类推。这种过程看起来很像 Reduce 操作，事实上 Reduce 操作可以看着 Fold 操作的一个特例，只是初始值为集合第一个元素和第二个元素的计算结果。

Fold 操作也有从左和从右两个版本

```scala
val foldLeftResult = numbers1.foldLeft(2)((n1, n2) => n1 - n2)
println(s"foldLeft => $foldLeftResult") //  -19

val foldRightResult = numbers1.foldRight(0)((n1, n2) => n1 - n2)
println(s"foldRight => $foldRightResult") //  -3
```


### Filter 操作

Filter 操作通过对集合中的每一个元素应用一个返回值是布尔值的函数来获得一个返回值都是 `true` 的新的集合.

```scala
val filterResult = numbers1.filter(_ % 2 == 0)
println(s"filter => $filterResult") //  List(2, 4, 6)
```

### Collect 操作

Collect 操作接收偏函数并返回偏函数处理的分组的结果集。

```scala
val collectResult = numbers1.collect(PartialFunction[Int, Option[Int]] {
	case x if x % 2 == 0 => Some(x)
    case _ => None
})
println(s"collect => $collectResult") //  List(None, Some(2), None, Some(4), None, Some(6))
```

### ForEach 操作

ForEach 操作用于对集合进行遍历。

```scala
numbers1.foreach(println)
```

### Parallel 操作

集合操作默认都是单线程的。但是可以使用 `par()` 方法获得进行并发的流式操作的能力，并发使用的是 `ForkJoinPool`。

```scala
numbers1.par.foreach(println)
```

Scala 中的并发流式操作和 Java 并不相同。虽然当前控制流中的操作是并发执行的，但是只有所有元素计算完毕后才会进行下一个控制流。且并发本身是不会被传递。而不像 Java 一个元素完成计算后就直接进入下一个控制流。

```scala
val parallelResult = numbers1.par.map((x) => {
    println(s"stream first: $x on ${Thread.currentThread().getName}")
    x
}).map((x) => {
    println(s"stream second: $x on ${Thread.currentThread().getName}")
    x
}).map((x) => {
    println(s"stream third: $x on ${Thread.currentThread().getName}")
    x
})
println(parallelResult)
```



## Kotlin

### Map 操作

Map 操作用于对集合中的每一个输入元素应用指定的函数从而得到另一种输出。输入类型和输出类型可以不一致。


```kotlin
val numbers1 = listOf(1, 2, 3, 4, 5, 6)
val numbers2 = listOf(10, 20, 30)
val numbers3 = listOf(numbers1, numbers2)
//  Map
val mapResult: List<Int> = numbers1.map { it * it }
println("map => $mapResult")    //  [1, 4, 9, 16, 25, 36]
```

### FlatMap 操作

FlatMap 操作用于对输入元素进行抽出，得到扁平化的输出。

```kotlin
val flatMapResult: List<Int> = numbers3.flatMap { it.map { it * 10 } }
println("flatMap => $flatMapResult") //  [10, 20, 30, 40, 50, 60, 100, 200, 300]         
```

如果只是想简单的抽出的话，可以直接使用更简单的 `flatten()` 方法。

```kotlin
println("flatten => ${numbers3.flatten()}") //  [1, 2, 3, 4, 5, 6, 10, 20, 30]
```

### Zip 操作

Zip 操作用于连接两个集合，新的集合以最短的集合为准。

```kotlin
val zipResult1: List<Pair<Int, Int>> = numbers1.zip(numbers2)
println("zip1 => $zipResult1")  //  [(1, 10), (2, 20), (3, 30)]

val zipResult2: List<Pair<Int, Int>> = numbers2.zip(numbers1)
println("zip2 => $zipResult2")  //  [(10, 1), (20, 2), (30, 3)]
```

### Reduce 操作

Reduce 操作用于对集合中的每一个元素进行指定的计算从而得到计算结果。计算的过程是将第一个元素和第二个元素进行计算，得到的结果和第三个元素再进行计算，以此类推。

Kotlin 有两个个版本的 Reduce 操作，默认的版本从左开始。

```kotlin
val reduceLeftResult = numbers1.reduce { n1, n2 -> n1 - n2 }
println("reduce => $reduceLeftResult")  //  -19

val reduceRightResult = numbers1.reduceRight { n1, n2 -> n1 - n2 }
println("reduceRight => $reduceRightResult")    //  -3
```

### Fold 操作

Fold 操作是给定一个初始值，然后集合中的第一个元素与初始值进行计算，计算结果再与集合中的下一个元素进行计算，以此类推。这种过程看起来很像 Reduce 操作，事实上 Reduce 操作可以看着 Fold 操作的一个特例，只是初始值为集合第一个元素和第二个元素的计算结果。

Fold 操作也有从左和从右两个版本

```kotlin
val foldLeftResult = numbers1.fold(2) { n1, n2 -> n1 - n2 }
println("foldLeft => $foldLeftResult")  //  -19

val foldRightResult = numbers1.foldRight(0) { n1, n2 -> n1 - n2 }
println("foldRight => $foldRightResult")    //  -3
```


### Filter 操作

Filter 操作通过对集合中的每一个元素应用一个返回值是布尔值的函数来获得一个返回值都是 `true` 的新的集合.

```kotlin
val filterResult = numbers1.filter { it % 2 == 0 }
println("filter => $filterResult")  //  [2, 4, 6]
```

### ForEach 操作

ForEach 操作用于对集合进行遍历。

```kotlin
numbers1.forEach { println(it) }
```

### Parallel 操作

Kotlin 目前并没有并行集合，不过官方计划在将来实现。



## 总结

- 除了 Groovy，其它语言都支持大量集合的函数式操作的 API，但是 Java 必须为 1.8 以上
- Java 1.8 和 Scala 支持并行集合，但是效果完全不同

---

项目源码见 [JGSK/_35_collection_function](https://github.com/SidneyXu/JGSK)