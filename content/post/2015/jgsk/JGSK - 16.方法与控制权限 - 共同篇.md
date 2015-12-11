---
comments: true
date: 2015-12-11T12:12:21+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16.method-access-modifier"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法与与控制权限 - 共同篇"
toc: true
topics:
- JGSK
---

## 共通篇

### 方法，函数与过程

这三种都是常听到的名字，概念也非常相近。一般来说函数 (Function) 是可重复调用可以有输出和输入的代码块。方法 (Method) 是定义在类中，作为类的成员的函数。过程(Subroutine)即没有返回值的函数。也就是说函数是基础，其它两种只是函数的特例。

由于这些名词容易混淆，在 Java 中统一都使用方法这个名词。而在 Kotlin 中使用关键字 `fun` 即表示 Kotlin 中使用的是函数这个名词。为了方便起见，本系列都使用方法进行描述，只有在牵涉到函数式编程时才使用函数这个名词。







