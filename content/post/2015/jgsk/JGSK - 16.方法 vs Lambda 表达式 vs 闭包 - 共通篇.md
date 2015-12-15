---
comments: true
date: 2015-12-11T12:12:21+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-closure-all"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法 vs Lambda 表达式 vs 闭包 - 共通篇"
toc: true
topics:
- JGSK
---

## 共通篇

### 方法，函数与过程

<!--more-->

这三种名词在编程中非常常见，其概念非常相近。一般来说函数 (Function) 是可重复调用带有有输出和输入的代码块。方法 (Method) 是定义在类中，作为类的成员的函数。过程(Subroutine)即没有返回值的函数。也就是说函数是基础，其它两种只是函数的特例。

由于这些名词容易混淆，在 Java 中统一都使用方法这个名词。而在 Kotlin 中使用关键字 `fun` 即表示 Kotlin 中使用的是函数这个名词。为了方便起见，本系列都使用方法这个名字进行描述，只有在牵涉到函数式编程时才使用函数这个名词。

### Lambda 表达式

所谓的 Lambda 表达式其实就是一个匿名函数。

### SAM 类型

SAM （Single Abstract Method）是有且仅有一个抽象方法的类型，该类型可以是抽象类也可以是接口。

### 闭包

闭包是一种带有自由变量的代码块，其最显著的特性就是能够扩大局部变量的生命周期。

#### 闭包与 Lambda 表达式

闭包与 Lambda 表达式的概念非常容易让人混淆，但两者确实是不同的东西。Lambda 表达式是匿名函数，而闭包则是实现函数是第一类对象的一种手段。两者的直接关系是 Lambda 表达式可以作为闭包的一种表现形式，但闭包除了 Lambda 表达式也可以表现为多种形式，这也就是在各种语言中闭包的写法会有很多差别的原因。

#### 闭包与方法

闭包和方法的最大区别是方法执行完毕后其内部的变量便会被释放，而闭包不会。闭包可以进行嵌套，而方法不行。




