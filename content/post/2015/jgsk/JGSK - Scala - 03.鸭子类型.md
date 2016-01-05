---
comments: true
date: 2015-12-18T00:31:35+08:00
description: ""
draft: false
keywords:
- scala
slug: "jgsk-scala-03-duck-type"
tags:
- scala
title: JGSK - Scala - 03.鸭子类型
toc: true
topics:
- JGSK
---

## 鸭子类型

Scala 中的鸭子类型其实就是结构类型。通过使用鸭子类型可以限定方法的参数只需要包含某种结构就行，而无需像 Java 一样为所有能传入方法的类型定义一个统一的父类。

<!--more-->

定义一个包含鸭子类型作为参数的方法

```scala
def show(obj: {val name: String}): Unit = {
    println(s"name is ${obj.name}")
}
```

其中 `{val name: String}` 就是鸭子类型，要求传入的类型必须包含一个名为 `name` 的成员变量。

定义两个符合条件的类

```scala
class Person(val name: String)
class Currency(val name: String)
```

调用方法

```scala
val peter = new Person("Peter")
show(peter) //  name is Peter

val dollar = new Currency("Dollar")
show(dollar) //  name is Dollar
```

如果使用 Java 的话，必须为 `Person` 和 `Currency` 类创建一个父类并且作为 `show()` 的参数才能实现此功能。

使用鸭子类型时甚至可以像以下例子连类都不用预先定义

```scala
show(new {
     val name = "foobar"
 }) // name is foobar
```
