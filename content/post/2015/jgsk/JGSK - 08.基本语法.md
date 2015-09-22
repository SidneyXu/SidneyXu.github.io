---
comments: true
date: 2015-09-17T07:55:06+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-08-gramma"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 08.基本语法
toc: true
topics:
- JGSK
---

## 共通篇

### 注释

四种语言都支持以下三种注释

```java
//  单行注释
/*
    多行注释
 */
/**
 * JavaDoc
 */
```

<!--more-->

## Java 篇

### 操作符

支持四则运算，赋值(=)，运算后赋值(+=)，递增递减(++, --)，三元操作符(?true:false)，逻辑操作符，关系操作符等常用操作符。

### 调用方法

语法

```
方法名(参数列表)
```

例

```java
add(1, 2)
```

### 相等

Java 使用 `==` 表示基本类型的值相等及引用类型的地址相等，用方法 `equals()` 表示引用类型的内容相同。不少 Java 的内置类都实现了自己的 `equals()` 方法，如果是自己建立的类则要实现自己的 `equals()` 方法。

```java
Date x = new Date(100);
Date y = new Date(100);

System.out.println("x==y: " + (x == y));    //  false
System.out.println("x.equals(y): " + x.equals(y));  //  true
```

## Groovy 篇

### 操作符

基本同 Java。 但是 Groovy 多了一个猫王操作符(?:) 作为三元操作符的语法糖。

```groovy
String agentStatus = "Active"

//  三元操作符
String status = (agentStatus !=null && !"".equals(agentStatus)) ? "Active" : "Inactive"

//  猫王操作符
String status = agentStatus ?: "Inactive"
```

Groovy 还有一些其它操作符在以后的章节再讲。

### 调用方法

语法基本同 Java，但是在 Groovy 中如果方法的参数个数只有一个时，可以省略包围在参数列表上的小括号

```groovy
println "Hello World"
```

### 相等

Groovy 认为比较内容的使用场合要远远大于比较地址，所以 Groovy 中的 `=` 实际就是调用 `equals()` 方法。

```groovy
println("x==y: " + (x == y))    //  true
println("x.equals(y): " + x.equals(y))  //  true
```

此外 Groovy 在比较时对空指针做了判断，所以等号左边是空值并不会引发空指针异常。

```groovy
println(null == 10) //  false
```

如果你希望调用 Java 版本的 `=` 比较地址，可以调用 `is()` 方法。

```groovy
println("x.is(y): " + x.is(y))  //  false
```

## Scala 篇

### 操作符

Scala 支持 Java 中除了递增，递减之外的所有操作符，所以在 Scala 中不能使用 `i++` 这样的语句，而需要使用 `i += 1` 来实现同样的功能。

此外由于 Scala 中一切皆对象，所以 Scala 中的所有操作符其实就是方法名，即 `1 + 2` 实际上就是 调用 `1.+(2)` 的语法糖。像这种操作符位于两个操作数之间的操作符被称为中缀操作符，Scala 中使用中缀操作符时可以省略 `.操作符()`，所以 `1.+(2)` 可以简写成 `1 + 2`。

### 调用方法

与 Java 基本相同

### 相等

同 Groovy，`==` 调用的是 `equals()` 方法。但是 Groovy 比较地址用的是 `is()`，而 Scala 则是 `eq()`。

```scala
println("x.eq(y): " + x.eq(y)) //  false
```

## Kotlin 篇

### 操作符

基本同 Groovy，也拥有猫王操作符

### 调用方法

同 Java

### 相等

和 Groovy，Scala 一样，`==` 也是调用 `equals()`，比较地址使用的是 `identityEquals()`，三种语言这一方法名各不相同也是够了。

```kotlin
println("x.identityEquals(y): " + x.identityEquals(y)) //  false
```


## 总结

- 除了 Scala 之外，其它语言都支持 `++` ，`--`
- `Groovy` 和 `Kotlin` 支持猫王操作符
- 除了 Java 之外，其它语言 `==` 调用的都是 `equals()`，且比较地址的方法名三种语言都不一样


---

项目源码见 [JGSK/_08_grammar](https://github.com/SidneyXu/JGSK)
