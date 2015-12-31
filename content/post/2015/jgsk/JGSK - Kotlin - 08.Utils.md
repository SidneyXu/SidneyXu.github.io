---
comments: true
date: 2015-12-31T07:02:22+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "jgsk-kotlin-08-utils"
tags:
- kotlin
title: JGSK - Kotlin - 08.Utils
toc: true
topics:
- JGSK
---

## Utils

本章主要介绍一些 Kotlin 内置的工具类和方法。

<!--more-->

### Preconditions

#### 基础概念

Preconditions（前提条件）主要用于对方法中传入的参数进行基础校验。

#### require 和 check

`require` 用于检查布尔表达式是否为真。其本身接收两个参数，第一个为等待校验的布尔值，第二个为当校验不通过时显示的消息，该消息将用于构造一个 `IllegalArgumentException` 异常。

```kotlin
require(msg.isNotEmpty()) {
    "Message is empty!"
}
```

`check` 使用同 `require`，只是返回的为 `IllegalStateException` 异常。

#### requireNotNull 和 checkNotNull

`requireNotNull` 用于检查参数是否为空。其本身接收两个参数，第一个为等待校验的参数，第二个为当校验不通过时显示的消息，该消息将用于构造一个 `IllegalArgumentException` 异常。当校验通过后， `requireNotNull` 会将传入的参数作为返回值进行原样返回。

```kotlin
val result = requireNotNull(msg) {
    "Object is null!"
}
```

通用 `checkNotNull` 使用同 `requireNotNull`，只是返回的为 `IllegalStateException` 异常。


#### 各种校验实现的比较

不使用前提条件时，一般的校验方式为如下形式，通过 `if` 语句来实现

```kotlin
if (msg.isEmpty()) {
    throw IllegalArgumentException("Message is empty!")
}
```

使用 Java 内置的断言机制

```kotlin
assert msg.isNotEmpty()
```

Google 的 Guava 包种的校验方式

```java
Preconditions.checkArgument(msg.isNotEmpty(), "Message is empty!")
```

其中通过 `if` 语句进行校验通常需要大量重复的语句。通过断言机制的话则只能检查表达式是否为真，并且无法定义易读的错误消息，此外断言是可以被禁止的。使用 Guava 的方式在与 Kotlin 提供的方式类似，但是 Kotlin 的消息构造是惰性加载的，所以效率上要高于 Guava。

### NotImplementedError

一般而言在写程序时我们通常将有待实现的方法上加上注释 `// TODO` 来标示该方法，但是对于调用者来说除非查看源码否则他没有办法知道这种信息。Kotlin 提供了 NotImplementedError` 异常用于表示这种情况。除此之外，Kotlin 还提供了一个内部方法 `TODO()` 用于抛出这种异常。

```kotlin
TODO("Not implemented in current version")
```

### 计算执行时间

Kotlin 内部提供了工具方法 `measureTimeMillis()` 可以用于计算代码块的耗时时间。

```kotlin
val duration = measureTimeMillis {
    var counter = 0
    repeat(100000000) {
        counter++
    }
}
println("duration is $duration")
``` 



