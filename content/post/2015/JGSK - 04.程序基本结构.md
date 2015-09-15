---
comments: true
date: 2015-09-15T14:58:31+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-04-base-structure"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 04.程序基本结构"
toc: true
topics:
- JGSK
---

## 概述

本章主要介绍四种程序的基本程序结构和特性。
<!--more-->

## Java 篇

在 Java 目录下新建 `Hello.java`

```java
package com.bookislife.jgsk._04_hello;

public class Hello {

    public static void main(String[] args) {
        String hello = "Hello, Java!";
        System.out.println(hello);
    }
}
```

从这简单的程序可以看到 Java 程序有这么几个特点

- 文件后缀为 `.java`
- 程序入口为 `main()` 方法
- 变量类型定义在变量名左边
- 所有语句以 `;` 结尾

## Groovy 篇

在 Groovy 目录下新建 `Hello.groovy`

```groovy
package com.bookislife.jgsk.groovy._04_hello

class Hello {

    static void main(String[] args) {
        def hello = "Hello, Groovy!"
        println(hello)
    }
}
```

从这简单的程序可以看到与 Java 相比， Groovy 程序有这么几个特点

- 文件后缀为 `.groovy`
- 语法非常像 `java`
- 程序入口为 `main()` 方法，但是可以省略 `public` 关键字
- 变量无需指定类型
- 打印语句更加简单
- 所有语句结尾可以没有 `;` 

## Scala 篇

在 Scala 目录下新建 `Hello.scala`

```scala
package com.bookislife.jgsk.scala._04_hello

object Hello {

  def main(args: Array[String]) {
    val hello = "Hello, Scala!"
    println(hello)
  }
}
```

从这简单的程序可以看到与 Java 相比， Scala 程序有这么几个特点

- 文件后缀为 `.scala`
- 语法与 `java` 有不少区别
- 程序入口为 `main()` 方法，且声明为 `object Hello` 而不是 `class Hello`
- 变量无需指定类型，但与 Groovy 含义并不一样
- 打印语句更加简单
- 所有语句结尾可以没有 `;` 

## Kotlin 篇

在 Kotlin 目录下新建 `Hello.kt`

```kotlin
package com.bookislife.jgsk.kotlin._04_hello

fun main(args: Array<String>) {
    val hello = "Hello, Kotlin!"
    println(hello)
}
```

从这简单的程序可以看到与 Java 相比， Kotlin 程序有这么几个特点

- 文件后缀为 `.kt`
- 语法与 `java` 差异很大
- 程序入口为 `main()` 方法，但是 `main()` 方法独立存在，而不是定义在类中
- 变量无需指定类型，含义类似 `scala`
- 打印语句更加简单
- 所有语句结尾可以没有 `;` 

---

项目源码见 [JGSK/_04_hello](https://github.com/SidneyXu/JGSK)
