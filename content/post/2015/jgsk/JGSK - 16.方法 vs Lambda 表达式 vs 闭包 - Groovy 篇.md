---
comments: true
date: 2015-12-14T14:37:28+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-closure-groovy"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法 vsLambda 表达式 vs 闭包 - Groovy 篇"
toc: true
topics:
- JGSK
---


## Groovy 篇

### 方法

#### 定义方法

完整的 Groovy 方法定义语法为

<!--more-->

``` 
[访问控制符] [static] def 方法名(参数列表)
```

Groovy 也和 Java 一样有成员方法和静态方法之分。

成员方法表示类的对象的一种行为，声明时没有关键字 `static`。

```groovy
def add(x, y) {
    x + y
}
```

静态方法使用关键字 `static` 声明，属于类的行为，或称作类对象的行为，因此调用时无需创建任何对象。`main()` 方法就是最常见的静态方法。

```groovy
static def main(String[] args) {
}
```


#### Varargs

Groovy 表示变参的方式与 Java 一样，且变参也只能出现在参数列表的最后一个。

声明一个变参方法

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

#### 参数默认值

Groovy 支持参数默认值，但是一旦使用参数默认值时，参数列表的最后一个或最后几个参数都必须有默认值，即 `def foo(x, y, z ="bar")` 和 `def foo(x, y = "bar", z = "bar")` 都是合法的，但是 `def foo(x, y = "bar", z)` 则是非法的。

```groovy
static def say(name, word = "Hello") {
    println("$word $name")
}

say("Peter")
```

#### 返回值

Groovy 中由动态类型的存在，所以可以不声明返回值类型。并且在 Groovy 中方法的最后一个语句的执行结果总是回被返回（也适用于无返回值的时候），所以也无需 `return` 语句。

```groovy
def add(x, y) {
    x + y
}
```

### Lambda 表达式

Groovy 目前还不支持 Java 1.8 的特性，所以 Java 中的 Lambda 表达式和对应的函数式接口无法在 Groovy 中直接使用。但是 Groovy 本身支持闭包，且闭包就是以 Lambda 表达式的形式存在的，所以闭包和 Lambda 合在一节讲。

### 闭包

#### 概念

闭包是一种带有自由变量的代码块，其最显著的特性就是能够扩大局部变量的生命周期。与 Java 不同，Groovy 支持真正的闭包。

#### 创建一个闭包

由于闭包是个代码块，所以一般意义上最简单的闭包形式如下

```groovy
{ println("foo") }
```

不过由于 Java 的普通代码块也是这样的形式，所以为了避免混淆，以上闭包必须写成如下形式

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

Groovy 中定义闭包实际是定义了一个继承自 `Closure` 类的匿名内部类，执行闭包实际是执行该类的实例的方法。这一点与 Java 非常相似。

#### 字面量

闭包可以存储在一个变量中，这一点是实现函数是一等公民的重要手段。

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

闭包的参数可以和方法的参数一样拥有多个参数及默认值

```groovy
def plus = { int x, int y = 1 ->
    println "$x plus $y is ${x + y}"
}
```

#### it

`it` 是个隐式参数，当闭包只有一个参数时，使用 `it` 可以直接指代该参数而不用预先声明参数列表。

```groovy
def greeting = { "Hello, $it!" }
println(greeting("Peter"))
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

#### 自执行闭包

自执行闭包即定义闭包的同时直接执行闭包，一般用于初始化上下文环境，Javascript 中常使用这种方法来初始化文档。

定义一个自执行的闭包

```groovy
{ int x, int y ->
    println "$x plus $y is ${x + y}"
}(1, 3) //  1 plus 3 is 4
```

