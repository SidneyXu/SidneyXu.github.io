---
comments: true
date: 2015-12-29T12:22:04+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "jgsk-kotlin-05-high-order-function"
tags:
- kotlin
title: "JGSK - Kotlin - 05.高阶函数"
toc: true
topics:
- JGSK
---


## 高阶函数

### 函数字面量

<!--more-->

所谓的函数字面量指的将函数本身赋值给一个变量。通过函数字面量，可以使函数本身表现得和普通变量一样。

定义一个函数字面量

```kotlin
 val sum = { x: Int, y: Int -> x + y }
```

Kotlin 在定义函数字面量时支持参数的类型推断，只要确定了返回值的类型，编写参数时就可以省略类型。

``` kotlin
val sum2: (Int, Int) -> Int = { x, y -> x + y }
```

以上函数的返回值类型为 `(Int, Int) -> Int`，表示函数有两个整型的输入和一个整型的返回值，所以定义参数 `x` 和 `y` 时可以省略参数类型。

Scala 中实现函数字面量的方法比较特别，是通过一个名为 `FunctionN` (n 为 0 到 22 的整数)的特质的 `apply()` 来实现的。其中 `Functon0` 代表没有参数，`Function1` 代表 1 个参数，以此类推。

所以以上 `sum2` 实质上是

```scala
val sum2: Function2[Int, Int, Int] = new Function2[Int, Int, Int] {
  override def apply(x: Int, y: Int): Int = x + y
}
```


### 高阶函数

高级函数指的是将函数本身作为参数传递给另一个函数或者一个函数返回值是另一个函数。正是由于函数字面值的存在，高阶函数才能得以实现。

#### 函数作为参数

定义一个参数为函数类型的函数

```kotlin
fun add10(f: (Int) -> Int) = f(10)
```

以上定义了一个函数 `add10()`，该函数接收一个以整型为输入并返回整型的函数类型参数，在函数体中会调用传入的函数并向其传入参数 10。

调用以上方法

```kotlin
add10({ it + 2 })   //  12
```

以上代码可以简写成以下形式，省略掉参数外的括号

``` kotlin
add10 { it + 2 }   //  12
```


#### 函数作为返回值

```kotlin
fun scale(factor: Double) = { x: Double -> x * factor }
```

### 柯里化

柯里化 (Currying) 指的是将一个接收多个参数的函数分解成多个接收单个参数的函数的一种技术。令人惊讶的是 Kotlin 居然也没有柯里化的语法糖，所以必须自己实现层层嵌套的比较丑陋的语法结构。

``` kotlin
def show2(String prefix) {
    return { String msg ->
        return { String postfix ->
            return prefix + msg + postfix
        }
    }
}
show2("(")("foobar")(")"
```

### 函数的部分应用

函数的部分应用 (Function Partial Application) 指的是向一个接收多个参数的函数传入部分参数从而获得一个接收剩余参数的新函数的技术。

Kotlin 目前没有提供部分应用的语法糖。

### 扩展函数作为参数

由于 Kotlin 支持函数扩展，所以可以将扩展的函数作为函数的参数。

例

```kotlin
fun accept(dec: Int.(other: Int) -> Int): Int {
    //  定义了一个 Receiver
    val i = 100
    return i.dec(10)
}
```

以上 `accept()` 接收一个以 `Int` 类型作为 Receiver 的函数，该函数接收一个 `Int` 类型的参数并且返回 `Int` 类型。在 `accept()` 中定义了一个 值为 `100` 的 Receiver。

调用该函数

```kotlin
val a = accept { x ->
    this + x
}
println(a)  //  110
```

需要特别注意其于将函数作为参数时的区别

```kotlin
fun accept2(f: (Int) -> Int): Int {
    return f(10)
}

val b = accept2 { x ->
    x + 10
}
println(b)  //  20
```


