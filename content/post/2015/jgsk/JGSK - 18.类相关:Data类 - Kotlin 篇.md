---
comments: true
date: 2015-09-22T12:28:25+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-18-class:data-kotlin"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 18.类相关:Data类 - Kotlin 篇"
toc: true
topics:
- Dev
---

## Data Class

### 特点

- Data 类是使用关键字 `data` 声明的类
- Data 类默认基于构造方法实现了 `toString()`, `componentN()`, `copy()`, `equals()` 和 `hashCode()` 方法，不在构造方法中定义的属性不会产生在 `toString()` 结果中。
- Data 类可以直接使用 `==` 进行比较，同样不在构造方法中定义的属性不会用在比较上
- Data 类只表示数据，不能拥有行为，所以通常来说我们没有必要为 Data 类构建类体

<!--more-->

### 定义 Data 类

大部分情况下没有必要构建类体，所以省略大括号

```kotlin
data class Customer(var name: String, var email: String, var friends: ArrayList<String>)
```

### 使用 Data 类

#### 创建对象

```kotlin
val peter = Customer("Peter", "peter@example.com", arrayListOf("Jane", "Tom"))
```

#### 复制对象

Data class 默认实现了 `copy()` 方法，可以进行属性的复制，该复制仅为浅复制

```kotlin
val tom = peter.copy()
val jane = peter.copy(name = "")
peter.friends.add("Tina")

println(peter)  //  Customer(name=Peter, email=peter@example.com, friends=[Jane, Tom, Tina])
println(tom)    //  Customer(name=Peter, email=peter@example.com, friends=[Jane, Tom, Tina])
println(jane)   //  Customer(name=, email=peter@example.com, friends=[Jane, Tom, Tina])
```

以上例子我们可以看到修改了 `peter` 的 `friends` 列表后 `jane` 和 `tom` 的 `friends` 列表也改变了，证明复制的仅是引用，不是值本身。

#### ComponentN

```kotlin
val (name, email) = peter
println("name=$name,email=$email")
```


---

项目源码见 [JGSK/_18_dataclass](https://github.com/SidneyXu/JGSK)

