---
comments: true
date: 2015-09-22T12:25:00+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-18-class:caseclass-scala"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 18.类相关:样本类 - Scala 篇"
toc: true
topics:
-  JGSK
---

## 样本类 Case Class

### 特点

- 样本类是使用关键字 `case class` 声明的类。它可以隐式调用构造方法进行初始化，样本类没有副构造方法。
- 样本类默认基于构造方法实现了 `toString()`, `equals()` 和 `hashCode()` 方法，不在构造方法中定义的属性不会产生在 `toString()` 的结果中。
- 样本类可以直接使用 `==` 进行比较，同样不在构造方法中定义的属性不会用在比较上
- 样本类被大量使用在模式匹配中

<!--more-->

#### 定义样本类

```scala
case class Number(n: Int) {
  var comment: String = "Default Comment"
  def eval: Int = n
}

case class Sum(n1: Number, n2: Number) {
  var comment: String = "Default Comment"
  def eval: Int = n1.eval + n2.eval
}
```

以上定义了两个样本类，其中样本类 `Sum` 用于计算两个样本类 `Number` 的和。

#### 使用样本类

样本类在初始化时可以隐式调用构造方法，所以无需使用 `new` 关键字。

```scala
val n = Number(1)
```

样本类默认实现了 `toString()` 方法，该方法覆盖了构造方法中的所有参数，而类中定义的变量不会被写到 `toString()` 方法中

```scala
println(n.toString)
```

样本类默认实现了 `equals()` 方法，该方法覆盖了构造方法中的所有参数，而类中定义的变量不会被写到 `equals()` 方法中

```scala
val result1 = Sum(Number(10), Number(20))
result1.comment = "result1"
val result2 = Sum(Number(10), Number(20))
result2.comment = "result2"
println(result1 == result2) //  true
```

---

项目源码见 [JGSK/_18_case_class](https://github.com/SidneyXu/JGSK)


