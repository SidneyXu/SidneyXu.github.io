---
comments: true
date: 2015-12-09T09:59:58+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "JGSK-Kotlin-02-extension"
tags:
- kotlin
title: JGSK - Kotlin - 02.Extension
toc: true
topics:
- JGSK
---

## Extension

Extension 是 Kotlin 中一个非常强大的功能，主要用于对已经定义好的类的行为或者属性进行扩展，这一特性非常类似 Javascript 中的 Prototype。

<!--more-->

### 扩展类的行为

先定义一个需要被扩展的类

```kotlin
class Num(var x: Int) {
    fun add(y: Int) {
        x += y
    }
}
```

以上定义一个 `Num`，该类包含一个方法 `add()`，用于将内置的变量与一个参数进行相加。

接下来扩展该类，为其添加一个将内置变量与两个参数进行相加的方法，语法为 `类.方法(){}`

```kotlin
fun Num.add(a: Int, b: Int) {
    this.x += a + b
}
```

接着就可以正常进行调用了

```kotlin
var n: Num = Num(10)
n.add(4)
n.add(1, 3)
println(n.x)    //  18
```

扩展功能不仅可以用于自定义的类，也可以用于内置的类。但是需要注意如果添加的扩展的方法与类中已存在的方法同名时，则该类的实例调用的始终是类中已定义的方法，而不会调用扩展的方法。

例

```kotlin
class Num(var x: Int) {
    fun foo() {
        println("foo in Num")
    }
}
fun Num.foo() {
    println("foo in extension")
}
n.foo()         //  foo in Num
```

### 扩展类的属性

扩展类的属性用法基本同扩展类的行为。以下展示为内置类 List 添加一个能够直接访问其第二个元素的新的属性

```kotlin
val <T> List<T>.second: T
    get() = get(1)
```

调用该属性

```kotlin
val l: List<Int> = listOf(1, 2, 3)
println(l.second)  //  2
```

### 扩展伴生对象

扩展伴生对象时使用方式略微不同，需要添加中间类 `Companion`

定义一个包含伴生对象的类

```kotlin
class MyClass {
    companion object {}
}
```

扩展其伴生对象

```kotlin
fun MyClass.Companion.foo() {
    println("foo in companion object")
}
```

调用扩展的方法

```kotlin
MyClass.foo()
```



## 总结

- Kotlin 中可以扩展已经定义的类的行为和属性。
- 扩展的行为或属性如果与类中已定义的成员同名则会被忽略。

---

项目源码见 [JGSK/k02_extension](https://github.com/SidneyXu/JGSK)

