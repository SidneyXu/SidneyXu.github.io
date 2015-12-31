---
comments: true
date: 2015-09-18T06:40:38+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-12-loop"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 12.循环
toc: true
topics:
- JGSK
---



## Java 篇

### For

`for` 语句可以说是最常用的循环语句了。Java 支持普通的 `for` 语句以及增强型的 `for-each` 语句。

for 语句

``` java
for (int i = 0; i < 3; i++) {
    System.out.println("Repeat For " + i);
}
```

<!--more-->

for-each 语句

``` java
int[] arr = {1, 2, 3};
for (int a : arr) {
    System.out.println("Repeat For-Each " + a);
}
```

### While

`while` 是另一种常用的循环语句，有 `while` 和 `do..while` 两种形式。

while

``` java
while (i < arr.length) {
    System.out.println("Repeat While " + arr[i++]);
}
```

do..while

``` java
do {
    i++;
    System.out.println("Repeat Do-While " + i);
} while (i < arr.length);
```

### Continue 和 Break

Continue 和 Break 都用于进行流程控制。Continue 用于跳过本次循环，直接进入下次循环。Break 用于跳出当前循环。

Continue 

```java
for (int i : Arrays.asList(1, 2, 3, 4, 5)) {
    if (i == 3) continue;
    System.out.println("Continue " + i);    //  1 2 4 5
}
```

Break

```java
for (int i : Arrays.asList(1, 2, 3, 4, 5)) {
    if (i == 3) break;
    System.out.println("Break " + i);   //  1 2
}
```



## Groovy 篇

### For

Groovy 中的 `for` 语句基本与 Java 一致。

``` groovy
for (def i = 0; i < 3; i++) {
  println "Repeat For ${i}"
}

def arr = [1, 2, 3]
for (def a : arr) {    
  println("Repeat For-Each ${a}")
}
```

### While

Groovy 没有 `do..while`  语句，只有 普通的`while` 语句。

``` groovy
while (i < arr.size()) {    
  println("Repeat While ${arr[i++]}")
}
```

### For-Range

Groovy 中有一个 Java 中没有的概念：Range，表示一个有序的范围，具体在下节会介绍。Range 结合 `for` 语句可以形成更加简便，直观的循环语句。

构造一个 Range 非常简单，`n1..n2` 就可以表示一个简单的 Range 对象

以下例子表示从 0 循环到 3，每次步长为1，左右都是闭区间，相比较普通的 `for` 循环，这种做法更加优美

``` groovy
for (a in 0..3) {    
  println "Repeat Range ${a}"
}
```

当然倒序也是可以的，步长为被自动修正为 -1。

```groovy
for (a in 3..0) {    
  println "Repeat Reverse-Range ${a}"
}
```language
```

也可以通过 `n1..n2` 这样的语法来指定左闭右开。

```groovy
for (a in 0..<3) {    
  println "Repeat Range ${a}"
}
```

### Continue 和 Break

Continue 和 Break 都用于进行流程控制。Continue 用于跳过本次循环，直接进入下次循环。Break 用于跳出当前循环。

Continue 

```groovy
for (i in 1..5) {
    if (i == 3) continue
    println("Continue $i")
}
```

Break

```groovy
for (i in 1..5) {
    if (i == 3) break
    println("Break $i")
}
```



## Scala 篇

### For

 Scala 的 `for` 语句与其它语言都不同，没有最基本的 C 风格的 `for (;;)` 语句，但是却有类似的 `for-each` 语句。

``` scala
val arr = Array(1, 2, 3)
for (a <- arr) {  
  println(s"Repeat For-Each $a")
}
```

也可以基于索引进行遍历

``` scala
for (i <- arr.indices) {  
  println(s"Repeat For-Each ${arr(i)}")
}
```

当然 Scala 的 `for` 语句不可能如此简单，更强大的功能在接下来会提到。

### While

Scala 支持基本的 `while` 和 `do-while` 语句。

while

``` scala
val arr = Array(1, 2, 3)
var i = 0
while (i < arr.length) {  
  println(s"Repeat While ${arr(i)}")  i += 1
}
```

do-while

```
do {  
  i += 1  
  println(s"Repeat While $i")
} while (i < 5)

```

### Power For

#### Range

同 Groovy 一样，Scala 更高级的 `for` 循环也是通过 `Range` 对象来实现的。
Scala 使用方法 `to()` 和 `until()` 来创建 `for` 循环，后者是前者的倒序形式，且使用 `for` 循环时循环变量无需预先定义，直接使用就可以了。

For-To

```scala
for (i <- 0 to 3) {  
  println(s"Repeat For $i")
}
```

因为 Scala 一切皆对象，所以以上的 `0 to 3` 实际方法 `0.to(3)` 简写形式，且循环变量 `i` 无需预先定义为 `var i`。

For-Until

``` scala
for (i <- 0 until 3) {  
  println(s"Repeat For-Until $i")
}
```

指定步长

使用关键字 `by` 可以在循环时指定步长

``` scala
for (i <- 0 to 5 by 2) {  
  println(s"Repeat For $i")
}
```

此外，Scala 在一个 `for` 循环中可以同时对多个变量进行循环，每个变量间以符号 `;` 进行分隔。

``` scala
for (i <- 0 to 3; j <- 2 to 4) {
  println(s"Repeat For $i, $j")
}
```

#### Guard

Scala 的 `for` 循环中可以插入 `if` 语句用于在循环中进行一些逻辑判断，这种 `if` 语句被称作为 "guard"（守护者）。

``` scala
val list = List(1, 2, 3)
for (i <- list if i != 2; if i > 1) {  
  println(s"Repeat with Guard $i")
}
```

#### Yield

`yield` 用于从 `for` 循环抽取所有符合特定条件的值，这种 `for` 循环被称作为 for 推导式。

``` scala
val list = List(1, 2, 3)
val ret = for {i <- list 
  if i != 3
} yield i
println(s"Repeat with Yield $ret")
```

以上返回结果 `ret` 为一个 `List` 对象，其内容为所有符合循环条件的 `i` 的值，即 `1, 2, 4, 5`。

### Continue 和 Break

Scala 和其它语言不同，其并没有提供 `break` 和 `continue` 作为关键字。直到 Scala 2.8 以前都必须手动实现这些功能，Scala 2.8 以后引入了 `Breaks` 工具类可以完成 Continue 和 Break 的功能。

`Breaks` 拥有两个方法：`breakable()` 用于定义控制的语句块，`break()` 用于从 `breakable()` 定义的语句块中跳出。

Continue 

循环体内的 `breakable` 实现了 Continue 的功能

```scala
for (i <- 1 to 5) {
  Breaks.breakable {
    if (i == 3) Breaks.break()
    println("Continue " + i)
  }
}
```

Break

包裹循环体的 `breakable` 实现了 Break 的功能

```scala
Breaks.breakable {
  for (i <- 1 to 5) {
    if (i == 3) Breaks.break()
    println("Break " + i)
  }
}
```



## Kotlin 篇

### For

同 Scala 一样，Kotlin 也没有传统的 `for` 语句，基本用法同 Scala。

``` kotlin
for ( a in arr) {    
  println("Repeat Power For $a");
}
```

基于索引

``` kotlin
for (i in arr.indices) {    
  println("Repeat For-indices ${arr[i]}")
}
```

### While

`while` 语句的用法也与 Scala 相似.

``` kotlin
val arr = intArrayOf(1, 2, 3)
var i = 0
while ( i < arr.size ) {
  println("Repeat While ${arr[i++]}")
}
i = 0
do {
  i++
  println("Repeat Do-While $i")
} while (i < 5)
```

### Range

Kotlin 的增强型 `for` 也基于 `Range` 对象。

``` kotlin
for (a in 0..3)
  println("Repeat Range $a")
```

倒序

``` kotlin
for (a in 3 downTo 0)
  println("Repeat Reverse-Range $a")
```

指定步长

``` kotlin
for (a in 0..5 step 2)
  println("Repeat Range with Step $a")
```

### Repeat

在 Kotlin 中还可以通过 `repeat` 操作来实现重复指定次数的操作。

```kotlin
repeat(3) {
    println("Repeat $it times")
}
```


### Continue 和 Break

Continue 和 Break 都用于进行流程控制。Continue 用于跳过本次循环，直接进入下次循环。Break 用于跳出当前循环。

Continue 

```kotlin
for (i in 1..5) {
    if (i == 3) continue
    println("Continue $i")
}
```

Break

```kotlin
for (i in 1..5) {
    if (i == 3) break
    println("Break $i")
}
```




## 总结

- Java 与 Groovy 的`for`语句相似，Scala 与 Kotlin 的`for` 语句相似
- Groovy 不支持 `do-while` 语句
- 除了 Java 之外，其它语言都支持基于 Range 的增强型 `for` 循环
- Scala 的 `for` 循环功能最强大
- 除了 Scala 之外，其它语言都支持 `break` 和 `continue`，Scala 则必须使用 `Breaks` 工具类


---

项目源码见 [JGSK/_12_loop](https://github.com/SidneyXu/JGSK)
