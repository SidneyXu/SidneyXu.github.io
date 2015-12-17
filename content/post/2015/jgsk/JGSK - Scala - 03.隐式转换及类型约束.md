---
comments: true
date: 2015-12-17T07:28:35+08:00
description: ""
draft: true
keywords:
- scala
slug: "jgsk-scala-03-implict"
tags:
- scala
title: JGSK - Scala - 03.隐式转换及类型约束
toc: true
topics:
- JGSK
---


## 隐式转换与类型约束

### 隐式转换

隐式转换是通过在指定的上下文环境中定义一个类型转换的函数来在必要时完成变量的自动类型转换。和子类到父类的自动类型转换不同，隐式转换是通过函数来实现的，所以原类型和目标类型可以没有任何关系。

Scala 中定义一个隐式转换函数只要在普通函数前加上关键字 `implicit` 就可以了。

例

定义一个对 `Int` 类型扩展的类 `SuperInt`

```scala
class SuperInt(val i: Int) {
    def triple = i * i * i
}
```

定义一个隐式转换函数，用于将 `Int` 类型的参数转换为 `SuperInt` 类型。

```scala
implicit def int2SuperInt(i: Int): SuperInt = new SuperInt(i)
```

定义一个使用 `SuperInt` 作为参数的函数

```scala
def showTripleResult(i: SuperInt): Unit = {
    println(s"Result is ${i.triple}")
}
```

使用隐式转换

```scala
val i: Int = 3
showTripleResult(i) //  Result is 27
```

以上例子中 `showTripleResult()` 函数需要 `SuperInt` 类型的参数，而传入的参数为 `Int` 类型，此时编译器会在上下文环境中查找是否有能够将 `Int` 类型参数转换为 `SuperInt` 类型的隐式转换函数。

事实上隐式转换是完全自动完成的，以下例子可以完全体现这点

```scala
println(i.triple) //  27
```

由于隐式转换的功能过于强大，所以必须限定隐式转换的上下文，否则的话会使代码非常难以阅读。最常见的作法是直接定义一个 `Object`，将转换函数都放在里面。

```scala
object Context {
    //  Implicit Conversion
    implicit def int2SuperInt(i: Int): SuperInt = new SuperInt(i)
}
```

然后在使用的地方先进行引用

```scala
import Context._
```


### 隐式参数
