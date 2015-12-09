---
comments: true
date: 2015-12-08T15:19:51+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "JGSK-Kotlin-01-mulitdecl"
tags:
- kotlin
title: "JGSK - Kotlin - 01.多重声明"
toc: true
topics:
- JGSK
---

## 多重声明

多重声明类似 Scala 中的 `unapply`，主要用于对对象进行拆箱。

实现多重声明只要在任意类内部定义了 `componentN()` 方法（N 为任意自然数）即可。

<!--more-->

```kotlin
class Person(val name: String, val age: Int) {
    fun component1(): String {
        return name
    }

    fun component2(): Int {
        return age
    }
}
```

以上类 `Person` 定义了两个 `componentN()` 方法。

如果需要进行拆箱的话则需要定义变量时将变量放在括号中。

```kotlin
val person = Person("Jane", 20)
val  (name, age) = person
println(name)   //  Jane
println(age)    //  20
```

以上便进行了完整的拆箱操作，但是拆箱用的变量数量也可以小于声明的 component 数量，即以下例子也是可以的。

```kotlin
val (n) = person
println(n)  //  Jane
```

但是以上括号不能省略，否则等于将 person 赋给变量 n。

## 总结

- 多重声明用于对对象进行拆箱操作。

---

项目源码见 [JGSK/k01_mulitdeclartion](https://github.com/SidneyXu/JGSK)
