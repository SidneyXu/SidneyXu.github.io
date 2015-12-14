---
comments: true
date: 2015-12-14T16:05:01+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-closure-kotlin"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法 vs Lambda 表达式 vs 闭包 - Kotlin 篇"
toc: true
topics:
- JGSK
---

## Kotlin 篇

### 方法

#### 定义方法

完整的 Kotlin 方法定义语法为

<!--more-->

``` 
[访问控制符] fun 方法名(参数列表) [:返回值类型] {}
```

Kotlin 可以省略变量定义的类型声明，但是在定义参数列表和定义返回值类型时则必须明确指定类型。

例

```kotlin
fun add(x: Int, y: Int): Int {
    return x + y
}
```

Kotlin 只有成员方法，没有静态方法，但是可以通过单例来实现静态方法的功能，具体内容见 Object 章节。

#### Varargs

Kotlin 使用 `vararg` 修饰参数来表示变参。

声明一个变参方法

```kotlin
class Calculator {
    fun sum(vararg n: Int) {
        println(n.sum())
    }
}
```

调用该方法

```kotlin
val calculator = Calculator()
calculator.sum(1, 2, 3)
```

#### 参数默认值

Kotlin 同 Scala 一样支持参数默认值，但是一旦使用参数默认值时，参数列表的最后一个或最后几个参数都必须有默认值。

```kotlin
fun say(name: String, word: String = "Hello") {
    println("$word $name")
}

say("Peter")
```

#### 返回值

Kotlin  同 Java 一样不会必须使用 `return` 语句来返回执行结果。

```kotlin
fun add(x: Int, y: Int): Int {
    return x + y
}
```

### Lambda 表达式

同 Scala 一样，闭包和 Lambda 也合在一节讲。

### 闭包

同 Scala 一样，Kotlin 也支持闭包，但是写法有些不同。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```kotlin
{ -> println("foo") }
```

#### 字面量

闭包可以存储在一个变量中，这一点是实现函数是一等公民的重要手段。

```kotlin
val excite = { word: String ->
    "$word!!"
}
```

#### 调用闭包

```kotlin
excite("Java")
```

或

```kotlin
excite.invoke("Kotlin")
```

#### 多参数

同 Scala 一样，Kotlin 中闭包的参数不能有默认值。

```kotlin
val plus = { x: Int, y: Int ->
    println("$x plus $y is ${x + y}")
}
```

#### it

同 Groovy 一样闭包只有一个参数时可以使用 `it` 直接指代该参数而不用预先声明参数列表。但是不像 Groovy 那么方便，Kotlin 中这一特性仅能用作传递作为参数的闭包中而不能用在定义闭包时。

以下闭包作为参数传递给方法 `filter`

```kotlin
val ints = arrayOf(1, 2, 3)
ints.filter {
    it > 3
}
```

以下定义闭包时指定 `it` 是非法的

```scala
val greeting = { -> println(it) }
```

#### Varargs

Kotlin 中闭包不支持变参


#### 闭包作为参数

```kotlin
fun max(numbers: Array<Int>, s: (Array<Int>) -> Int): Int {
    return s.invoke(numbers)
}
```

传入闭包

```kotlin
val maxValue = max(arrayOf(3, 10, 2, 1, 40)) {
    it.max()!!
}
```

#### 自执行闭包

自执行闭包即定义闭包的同时直接执行闭包，一般用于初始化上下文环境，Javascript 中常使用这种方法来初始化文档。

定义一个自执行的闭包

```kotlin
{ x: Int, y: Int ->
    println("$x plus $y is ${x + y}")
}(1, 3)    //  1 plus 3 is 4
```



