---
comments: true
date: 2015-09-20T20:46:05+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-and-closure"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 16.方法,Lambda 表达式与闭包
toc: true
topics:
- JGSK
---


## Groovy 篇

### 方法

#### 定义方法

完整的 Groovy 方法定义语法为

``` 
[访问控制符] [static] def 方法名(参数列表)
```

Groovy 也和 Java 一样有成员方法和静态方法之分。

#### Varargs

Groovy 表示变参的方式与 Java 一样 

```groovy
class Calculator {
    def sum(int ... n) {
        print(n.sum())
    }
}
```

调用该方法

```groovy
def calculator = new Calculator()
calculator.sum(1, 2, 3)
```

#### 方法的默认值

Groovy 支持方法默认值，且设置时最后一个或多个连续的参数都必须有默认值

```groovy
static def say(name, word = "Hello") {
    println("$word $name")
}

say("Peter")
```

如果以上 `say()` 方法替换成以下形式则是非法的，因为最后一个参数没有默认值

```groovy
static def say(name, word = "Hello", bye) {
    println("$word $name")
}
```

#### 返回值

Groovy 中由动态类型的存在，所以可以不声明返回值类型，且方法的最后一个语句的执行结果总被返回（也适用于无返回值的时候），所以也无需 `return` 语句。

```groovy
def add(x, y) {
    x + y
}
```

### 闭包

闭包指的是带有自由变量的代码块。对于 Java 程序员，闭包是一个非常难以理解的概念，这点只能慢慢领会。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```groovy
{ println("foo") }
```

不过由于 Java 的普通代码块也是这样的形式，所以为了避免混淆，通常写成如下形式

```groovy
{ -> println("foo") }
```

综上所述，闭包的语法为

``` 
{ 参数列表 -> 执行语句 }
```

例

```groovy
{ x, y ->
    println "$x plus $y is ${x + y}"
}
```

Groovy 中定义闭包实际是定义了一个继承自 `Closure` 类的匿名内部类，执行闭包实际是执行该类的实例的方法。

#### 字面量

闭包也可以存储在一个变量中

```groovy
def excite = { word ->
    "$word!!"
}
```

#### 调用闭包

```groovy
excite("Java")
```

或

```groovy
excite.call("Groovy")
```

#### 多参数

闭包也可以拥有多个参数

```groovy
def plus = { int x, int y = 1 ->
    println "$x plus $y is ${x + y}"
}
```

#### it

`it` 是个隐式参数，当闭包只有一个参数时，使用 `it` 可以指代该参数。

```groovy
def greeting = { "Hello, $it!" }
greeting("Peter")
```

#### Varargs

闭包也支持变参

```groovy
def contact = { String... args -> args.join(',') }
println(contact("Java", "Groovy", "Scala", "Kotlin"))
```

#### 闭包作为参数

由于闭包本质是 `Closure` 的子类，所以可以使用 `Closure` 作为参数的类型接收一个闭包

```groovy
static def max(numbers, Closure<Integer> closure) {}
```

进一步简化后

```groovy
static def max(numbers, cls) {
    cls(numbers)
}
```

传入闭包

```groovy
def maxValue = max([3, 10, 2, 1, 40]) {
    def list = it as List<Integer>
    list.max()
}
assert maxValue == 40
```

## Scala 篇

### 方法

#### 定义方法

完整的 Scala 方法定义语法为

``` 
[访问控制符] def 方法名(参数列表) [:返回值类型] [=] {}
```

Scala 虽然在定义变量时可以省略类型声明，但是在定义参数列表时必须指定类型。

例

```scala
def add(x: Int, y: Int): Int = {
  x + y
}
```

Scala 只有有成员方法，没有静态方法，但是可以通过单例来实现静态方法的功能，具体内容见 Object 章节。

#### 参数列表

Scala 中如果一个方法没有参数列表时，可以省略小括号，但是调用时也不能加上小括号。

```scala
def info(): Unit = {
  println("This is a class called Calculator.")
}
println(info())

def info2: Unit = {
  println("This is a class called Calculator.")
}
println(info)
```


#### Varargs

Scala 使用 `参数类型*` 表示变参

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

#### 方法的默认值

Scala 同 Groovy 一样也支持方法默认值，且设置时最后一个或多个连续的参数都必须有默认值

```scala
def say(name: String, word: String = "Hello"): Unit = {
  println(s"$word $name")
}

say("Peter")
```

#### 返回值

Scala 中总是会返回方法内部的最后一个语句的执行结果，所以无需 `return` 语句。如果没有返回值的话需要声明返回值类型为 `Unit`，且此时可以省略 `:Unit=`。

返回最后一行的执行结果

```scala
def add(x: Int, y: Int): Int = {
  x + y
}
```

无返回值的情况

```scala
def echo(): Unit = {}
```

可以简写为

```scala
def echo() = {}
```

### 闭包

Scala 也支持闭包的概念。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```scala
() => println("foo")
```

#### 字面量

闭包也可以存储在一个变量中

```scala
def excite = (word: String) =>
  s"$word!!"
```

#### 调用闭包

```scala
excite("Java")
```

#### 多参数

闭包也可以拥有多个参数

```scala
def plus =  (x: Int, y: Int) =>
  println(s"$x plus $y is ${x + y}")
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
var maxValue = max(Array(3, 10, 2, 1, 40), (numbers) => {
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



## Kotlin 篇


### 方法

#### 定义方法

完整的 Kotlin 方法定义语法为

``` 
[访问控制符] fun 方法名(参数列表)[:返回值类型]
```

Kotlin 和 Scala 一样没有静态方法。

#### Varargs

Kotlin 使用修饰符 `vararg` 表示变参

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

#### 方法的默认值

Kotlin 支持方法默认值，且设置时最后一个或多个连续的参数都必须有默认值

```kotlin
fun say(name: String, word: String = "Hello") {
    println("$word $name")
}

say("Peter")
```

#### 返回值

Groovy 中由动态类型的存在，所以可以不声明返回值类型，且方法的最后一个语句的执行结果总被返回（也适用于无返回值的时候），所以也无需 `return` 语句。

```groovy
def add(x, y) {
    x + y
}
```

当没有返回值时，返回类型可以声明为 `unit` 或省略。

```kotlin
fun echo(): Unit {}
fun echo2() {}
```

### 闭包

闭包指的是带有自由变量的代码块。对于 Java 程序员，闭包是一个非常难以理解的概念，这点只能慢慢领会。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```groovy
{ println("foo") }
```

不过由于 Java 的普通代码块也是这样的形式，所以为了避免混淆，通常写成如下形式

```groovy
{ -> println("foo") }
```

综上所述，闭包的语法为

``` 
{ 参数列表 -> 执行语句 }
```

例

```groovy
{ x, y ->
    println "$x plus $y is ${x + y}"
}
```

Groovy 中定义闭包实际是定义了一个继承自 `Closure` 类的匿名内部类，执行闭包实际是执行该类的实例的方法。

#### 字面量

闭包也可以存储在一个变量中

```groovy
def excite = { word ->
    "$word!!"
}
```

#### 调用闭包

```groovy
excite("Java")
```

或

```groovy
excite.call("Groovy")
```

#### 多参数

闭包也可以拥有多个参数

```groovy
def plus = { int x, int y = 1 ->
    println "$x plus $y is ${x + y}"
}
```

#### it

`it` 是个隐式参数，当闭包只有一个参数时，使用 `it` 可以指代该参数。

```groovy
def greeting = { "Hello, $it!" }
greeting("Peter")
```

#### Varargs

闭包也支持变参

```groovy
def contact = { String... args -> args.join(',') }
println(contact("Java", "Groovy", "Scala", "Kotlin"))
```

#### 闭包作为参数

由于闭包本质是 `Closure` 的子类，所以可以使用 `Closure` 作为参数的类型接收一个闭包

```groovy
static def max(numbers, Closure<Integer> closure) {}
```

进一步简化后

```groovy
static def max(numbers, cls) {
    cls(numbers)
}
```

传入闭包

```groovy
def maxValue = max([3, 10, 2, 1, 40]) {
    def list = it as List<Integer>
    list.max()
}
assert maxValue == 40
```