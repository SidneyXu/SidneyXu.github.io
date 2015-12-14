---
comments: true
date: 2015-12-14T14:59:46+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-closure-scala"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法 vs Lambda 表达式 vs 闭包 - Scala 篇"
toc: true
topics:
- JGSK
---

## Scala 篇

### 方法

#### 定义方法

完整的 Scala 方法定义语法为

<!--more-->

``` 
[访问控制符] def 方法名(参数列表) [:返回值类型] [=] {}
```

Scala 可以省略变量定义的类型声明和返回值类型，但是在定义参数列表时则必须明确指定类型。

例

```scala
def add(x: Int, y: Int): Int = {
  x + y
}
```

Scala 只有成员方法，没有静态方法，但是可以通过单例来实现静态方法的功能，具体内容见 Object 章节。

#### 参数列表

Scala 中如果一个方法没有参数列表时，可以省略小括号，但是调用时也不能加上小括号。

```scala
//  没有小括号
def info(): Unit = {
  println("This is a class called Calculator.")
}
println(info())

//  有小括号
def info2: Unit = {
  println("This is a class called Calculator.")
}
println(info)
```


#### Varargs

Scala 使用 `参数类型*` 表示变参。

声明一个变参方法

```scala
class Calculator {
  def sum(n: Int*) {
    println(n.sum)
  }
}
```

调用该方法

```scala
val calculator = new Calculator
calculator.sum(1, 2, 3)
```

#### 参数默认值

Scala 同 Groovy 一样支持参数默认值，但是一旦使用参数默认值时，参数列表的最后一个或最后几个参数都必须有默认值。

```scala
def say(name: String, word: String = "Hello"): Unit = {
  println(s"$word $name")
}

say("Peter")
```

#### 返回值

Scala 中总是会返回方法内部的最后一个语句的执行结果，所以无需 `return` 语句。如果没有返回值的话需要声明返回值类型为 `Unit`，并此时可以省略 `:Unit=`。

默认返回最后一行的执行结果

```scala
def add(x: Int, y: Int): Int = {
  x + y
}
```

无返回值的情况

```scala
def echo(): Unit = {}
```

无返回值时可以简写为以下形式

```scala
def echo() = {}
```

### Lambda 表达式

同 Groovy 一样，闭包和 Lambda 也合在一节讲。

### 闭包

同 Groovy 一样，Scala 也支持闭包，但是写法有些不同。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```scala
() => println("foo")
```

#### 字面量

闭包可以存储在一个变量中，这一点是实现函数是一等公民的重要手段。

```scala
val excite = (word: String) =>
  s"$word!!"
```

#### 调用闭包

```scala
excite("Java")
```

或

```scala
excite.apply("Scala")
```

#### 多参数

闭包的参数可以和方法的参数一样拥有多个参数，但是同 Groovy 不一样，Scala 中闭包的参数不能有默认值，且参数列表为多个时必须将参数包裹在小括号内。

```scala
val plus =  (x: Int, y: Int) =>
  println(s"$x plus $y is ${x + y}")
```

#### _

`_` 是个占位符，当闭包只有一个参数时，使用 `_` 可以直接指代该参数而不用预先声明参数列表。

```scala
val greeting = "Hello,  " + _
println(greeting("Peter"))
```


#### Varargs

Scala 中闭包不支持变参


#### 闭包作为参数

```scala
def max(numbers: Array[Int], s: (Array[Int]) => Int): Unit = {
  s.apply(numbers)
}
```

传入闭包

```scala
val maxValue = max(Array(3, 10, 2, 1, 40), (numbers) => {
  numbers.max
})
```

也可以使用如下方式进行简化

```scala
def max2(numbers: Array[Int])(s: (Array[Int]) => Int): Unit = {
  s.apply(numbers)
}

maxValue = max2(Array(3, 10, 2, 1, 40)) { numbers =>
  numbers.max
}
```

#### 自执行闭包

自执行闭包即定义闭包的同时直接执行闭包，一般用于初始化上下文环境，Javascript 中常使用这种方法来初始化文档。

定义一个自执行的闭包

```scala
((x: Int, y: Int) => {
  println(s"$x plus $y is ${x + y}")
})(1, 3)    //  1 plus 3 is 4
```



