---
comments: true
date: 2015-09-22T12:26:03+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-20-switch"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 20.switch 与模式匹配"
toc: true
topics:
- JGSK
---


## Java 篇

### Switch

- Java 中 switch 语句功能类似 if，但是 switch 主要用在多条件分支上。 
- switch 语句由 case 分支和 default 组成，case 分支用于检查条件，且参数必须为常量，default 用于执行当任何 case 分支都不匹配时执行的语句。
- switch 语句 在 Java 1.7 以前只支持基本类型，Java 1.7 开始支持 String
- 执行时 switch 语句会从上而下检查与 case 条件是否一致，一旦发现相等的分支，switch 语句就会执行该条 case 分支下的语句，并且之后会忽略下面的所有 case 分支，直到遇到 `break` 才会停止执行。

<!--more-->

例

```java
int i = 5 + 5;
switch (i) {
    case 1:
        System.out.println("i is 1");
    case 10:
        System.out.println("i is 10");
    case 100:
        System.out.println("i is 100");
    case 1000:
        System.out.println("i is 1000");
        break;
    case 10000:
        System.out.println("i is 10000");
    default:
        System.out.println("default");
}
```

以上语句的输出结果为

```
i is 10
i is 100
i is 1000
```

## Groovy 篇

### Switch

Groovy 的 switch 语句类似 Java，但是 Groovy 除了支持基本类型，也支持引用类型，包含，正则匹配等各种操作。

例

```groovy
static def testSwitch(var) {
    def result
    switch (var) {
        case 0:
            result = "Object equals"
            break
        case 11..20:
            result = "Range contains"
            break
        case [1, 2, 3]:
            result = "List contains"
            break
        case Float:
            result = "Class instance"
            break
        case { it % 3 == 0 }:
            result = "Closure"
            break
        case ~'[0-9]{3}':
            result = "Pattern match"
            break
        default:
            result = "Default"
            break
    }
    result
}

assert "Object equals" == testSwitch(0)
assert "Range contains" == testSwitch(15)
assert "List contains" == testSwitch(3)
assert "Class instance" == testSwitch(4.5f)
assert "Closure" == testSwitch(21)
assert "Pattern match" == testSwitch(910)
```

以上语句的输出结果为

```
i is 10
i is 100
i is 1000
```


## Scala 篇

### 模式匹配

Scala 没有 switch 语句，但是有类似功能的模式匹配语句。模式匹配外观上有些像 Java 的 switch 语句，但是没有 break 语句，功能也更加强大。

详细的示例

```scala
def matchObject(foo: Any): String = {
    val result = foo match {
      case 0 => "Object equals"
      case i if i == 10 || i == 11 => "Expression"
      case i: Int => s"Class instance holds $i"
      case List(1, 2, _*) => "Match the last element of a list"
      case Number(n) => s"Case class holds $n"
      case t: {def length: Int} => "Class structure contains method length"
      case _ => "Default"
    }
    result
}

println(matchObject(0)) //  Object equals
println(matchObject(10)) //  Expression
println(matchObject(20)) //  Class instance holds 20
println(matchObject(List(1, 2, 1000, 2000))) //  Match the last element of a list
println(matchObject(Number(1))) //  Case class holds 1
println(matchObject(List(3, 2, 1000, 2000))) //  Method contains
```

除了以上示例，模式匹配还可以用在 `try...catch` 语句中

```scala
def matchTryCatch: Unit = {
    try {
      throw new NullPointerException
    } catch {
      case e: NullPointerException => println("NullPointerException")
      case e: IndexOutOfBoundsException => print("IndexOutOfBoundsException")
    } finally {
      println("finally")
    }
}
```


## Kotlin 篇

### when 语句

Kotlin 也没有 switch 语句，但是有类似功能的 when 语句。when 语句也没有 break 语句。

详细的示例

```kotlin
private fun test(foo: Any): String {
    var result = when (foo) {
        0 -> "Object equals"
        3, 10 -> "Or"
        in 11..20 -> "Range contains"
        is Date -> "Class instance"
        !in 4..30 -> "Range not contain"
        else -> "Default"
    }
    return result
}

assert("Object equals" == test(0))
assert("Or" == test(3))
assert("Range contains" == test(11))
assert("Range not contain" == test(33))
assert("Class instance" == test(Date()))
```

除了以上示例，模式匹配还可以用来替代 `if-else` 语句

```kotlin
val x = 10
val y = 20
when {
    x + y == 5 -> println("x + y = 5")
    x + y == 10 -> println("x + y = 10")
    else -> println("x + y != 5 or 10")
}
```

## 总结

- Java 和 Groovy 支持 switch 语句，但是 Groovy 功能更强大
- Scala 支持模式匹配
- Kotlin 支持 when 语句


---

项目源码见 [JGSK/_20_switch_match](https://github.com/SidneyXu/JGSK)

