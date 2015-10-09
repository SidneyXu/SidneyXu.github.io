---
comments: true
date: 2015-10-04T21:26:32+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-27-generic-scala"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 27.泛型 - Scala篇
toc: true
topics:
- JGSK
---


## 泛型

### 基本概念

基本概念同 Java。

<!--more-->

### 创建泛型

类型参数使用 `[类型参数名]` 作为类型的占位符，而 Java 用的是 `<>`。

```scala
class Capture[A](val a: A) {
}
```

Scala 中最常用的占位符为 "A"。

### 使用泛型

```scala
val integerCapture = new Capture[Int](10)
val nint10:Int = integerCapture.a
val stringCapture = new Capture[String]("Hi")
val strHi:String = stringCapture.a
println(strHi)
```

以上分别用 `Int` 和 `String` 作为传入的类型参数，如果向这两个对象传入不符合的类型时编译器就会理解报错，此外取数据时也不用进行强制转换，比起没有泛型时要方便很多。

### 协变与逆变

如果 Capture<String> 被看做是 Capture<Object> 的子类型，则称这种特性为协变。相反情况则称为逆变。
在 Scala 中，这两种特性都是默认不支持的。

注意，函数的参数是逆变的，函数的返回值是协变的。

#### 使用协变

使用协变需要在类型前加上 `+`。

定义一个支持协变的类，协变类型参数只能用作输出，所以可以作为返回值类型但是无法作为入参的类型

```scala
class CovariantHolder[+A](val a: A) {
  def foo(): A = {
    a
  }
}
```

使用该类

```scala
var strCo = new CovariantHolder[String]("a")
var intCo = new CovariantHolder[Int](3)
var anyCo = new CovariantHolder[AnyRef]("b")

//  Wrong!! Int 不是 AnyRef 的子类
// anyCo = intCo
anyCo = strCo
```

#### 使用逆变

使用逆变需要在类型前加上 `-`。

定义一个支持逆变的类，逆变类型参数只能用作输入，所以可以作为入参的类型但是无法作为返回值类型

```scala
class ContravarintHolder[-A](val a: A) {
  def foo(p: A): Unit = {
  }
}
```

使用该类

```scala
var strDCo = new ContravarintHolder[String]("a")
var intDCo = new ContravarintHolder[Int](3)
var anyDCo = new ContravarintHolder[AnyRef]("b")

//  Wrong!! AnyRef 不是 Int 的超类
// strDCo = anyDCo
strDCo = anyDCo
```

### 类型通配符

概念与 Java 基本一致，只是 Scala 使用 `_` 作为通配符。

```scala
def foo2(capture: Capture[_]): Unit = {
}
```

### 类型参数边界

上边界用于限定类型参数一定是某个类的子类，使用符号 `<:` 指定。下边界用于限定类型参数一定是某个类的超类，使用符号 `>:` 指定。

上边界无法确定容器中保存的真实类型，所以无法向其中追加数据，但是可以获得边界类型的数据

```scala
def foo3(list: collection.mutable.MutableList[_ <: Num]): Unit = {
    //    list += new Num(4)
    val num = list.head
    println(num.number)
}
```

下边界可以追加边界类型的数据，但是获得数据都只能是 Any 类型

```scala
def foo4(list: collection.mutable.MutableList[_ >: Num]): Unit = {
    list += new Num(4)
    val num = list.head
    println(num.asInstanceOf[Num].number)
}
```

### 最小类型

Scala 中在表示边界时可以使用 `Nothing` 表示最小类型，即该类为所有类型的子类，所有可以写出以下代码。

```scala
def foo5(capture: Capture[_ >: Nothing]): Unit = {
}
```

---

项目源码见 [JGSK/_27_generics](https://github.com/SidneyXu/JGSK)








