---
comments: true
date: 2015-09-18T06:40:25+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-11-if-condition"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 11.条件表达式
toc: true
topics:
- JGSK
---


## Java 篇

### if

if 语句用于表达这样一种概念，在某种条件下执行一种操作，在另一种条件下执行另一种操作。
Java 中的 if 语句如果操作只有一行语句的话可以省略花括号，尽管很多书籍不建议使用这种方式。
<!--more-->

```java
int x = 1;
int y = 2;
if (x + y == 3) {
    System.out.println("x+y=3");
} else {
    System.out.println("x+y!=3");
}

if (x + y == 3)
    System.out.println("x+y=3");
else
    System.out.println("x+y!=3");
```

## Groovy 篇

Grooy 使用方式同 Java。

```groovy
def x = 1
def y = 2
if (x + y == 3) {
    println "x+y=3"
} else {
    println "x+y!=3"
}

if (x + y == 3)
    println "x+y=3"
else
    println "x+y!=3"
```

## Scala 篇

Scala 的使用方式也同 Java

```scala
val x = 1
val y = 2
if (x + y == 0) {
  println("x+y==3")
} else {
  println("x+y!=3")
}

if (x + y == 3)
  println("x+y==3")
else
  println("x+y!=3")
```

不过 Scala 中常常将第二种方式放在一行来写，这种写法牵扯到函数式编程的一些概念，以后会再提

```scala
if (x + y == 3) println("x+y==3") else println("x+y!=3")
```

## Kotlin 篇

Kotlin 用法同 Java。

```kotlin
val x = 1
val y = 2
if (x + y == 3) {
    println ("x+y=3")
} else {
    println ("x+y!=3")
}

if (x + y == 3)
    println ("x+y=3")
else
    println ("x+y!=3")
```

## 总结

- if 语句各种语言的语法基本一致


---

项目源码见 [JGSK/_11_if](https://github.com/SidneyXu/JGSK)
