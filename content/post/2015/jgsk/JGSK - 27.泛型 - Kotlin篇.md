---
comments: true
date: 2015-10-04T21:26:38+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-27-generic-kotlin"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 27.泛型 - Kotlin篇
toc: true
topics:
- JGSK
---


## 泛型

### 基本概念

基本概念同 Java。

<!--more-->

### 创建泛型

同 Java。

```kotlin
class Capture<T>(val t: T)
```

### 使用泛型

```kotlin
val integerCapture = Capture(10)
val nint10 = integerCapture.t
val stringCapture = Capture("Hi")
val str = stringCapture.t
```

### 协变与逆变

在 Kotlin 中，这两种特性都是默认不支持的。

注意，函数的参数是逆变的，函数的返回值是协变的。

#### 使用协变

使用协变需要在类型前加上 `out`，相比较 Scala 使用的 `+` 可能更能让人理解。

定义一个支持协变的类，协变类型参数只能用作输出，所以可以作为返回值类型但是无法作为入参的类型

```kotlin
class CovariantHolder<out A>(val a: A) {
    fun foo(): A {
        return a
    }
}
```

使用该类

```kotlin
var strCo: CovariantHolder<String> = CovariantHolder("a")
var anyCo: CovariantHolder<Any> = CovariantHolder<Any>("b")
anyCo = strCo
```

#### 使用逆变

使用逆变需要在类型前加上 `in`。

定义一个支持逆变的类，逆变类型参数只能用作输入，所以可以作为入参的类型但是无法作为返回值的类型

```kotlin
class ContravarintHolder<in A>(a: A) {
    fun foo(a: A) {
    }
}
```

使用该类

```kotlin
var strDCo = ContravarintHolder("a")
var anyDCo = ContravarintHolder<Any>("b")
strDCo = anyDCo
```


### 类型通配符

Kotlin 使用 `*` 作为通配符，而 Java 是 `？`，Scala 是 `_`。

```kotlin
fun foo2(capture: Capture<*>) {
}
```

### 类型参数边界

Kotlin 并没有上下边界这种说法。但是可以通过在方法上使用协变和逆变来达到同样的效果。

使用协变参数达到上边界的作用，这里的 `out` 很形象地表示了协变参数只能用于输出

```kotlin
fun foo3(list: MutableList<out Num>) {
    val num: Num = list.get(0)
    println(num)
}
```

使用逆变参数达到下边界的作用，这里的 `in` 很形象地表示了逆变参数只能用于输入

```kotlin
fun foo4(list: MutableList<in Num>) {
    list.add(Num(4))
    val num: Any? = list.get(0)
    println(num)
}
```


---

项目源码见 [JGSK/_27_generics](https://github.com/SidneyXu/JGSK)
