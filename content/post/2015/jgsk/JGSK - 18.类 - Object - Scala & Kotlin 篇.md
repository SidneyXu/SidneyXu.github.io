---
comments: true
date: 2015-09-22T12:23:33+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-18-class:object-scala&kotlin"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 18.类相关:Object - Scala & Kotlin篇
toc: true
topics:
- JGSK
---

## Scala 篇

### Object

#### 单例对象

在 Java 中实现单例对象通常需要自己实现一个类并创建 `getInstance()` 的方法然后在该方法里使用两次同步块或者使用更为优雅的基于 `enum` 的方式。而 Scala 中则更加简单，只要使用 `object` 声明就能创建一个单例对象。实际上之前我们创建的拥有 `main()` 方法的都是单例对象。

<!--more-->

```scala
object Singleton {
  private var num = 0
  def sequence() = {
    num += 1
    num
  }
}
```

在方法的章节我们已经知道了在 Scala 中是没有静态成员这一说的，但是有时我们可能又真的需要有这种机制。Scala 就是采用了使用单例对象来实现类似的静态成员。

例

```scala
assert(1 == Singleton.sequence())
assert(2 == Singleton.sequence())
assert(3 == Singleton.sequence())
```

可以看到这种方式和在 Java 中调用静态方法一模一样，但是 Scala 中这其实是调用了一个对象的方法。

通常来说，Scala 中的单例对象一般用于提供常量及共享不可变对象。

#### 伴生对象

我们知道使用 `class` 可以创建一个类，使用 `object` 可以创建一个单例对象，而类和单例对象是可以重名的，此时我们称该单例对象为此类的伴生对象。

伴生对象有以下特点

- 伴生对象可以用于让一个类即拥有实例化方法又有静态方法。
- 伴生对象可以用于创建同名类的实例。
- 伴生对象必须与同名类位于同一源文件中。
- 伴生对象与类可以互相访问各自的私有成员。

创建一个类

```scala
class Companion(private var balance: Int = 0) {
  val id = Companion.sequence()
}
```

创建该类的伴生对象

```scala
object Companion {
  private var num = 0

  def getInfo(account: Companion): String = {
    "Balance is " + account.balance
  }

  private def sequence(): Int = {
    num += 1
    num
  }

  def apply(initBalance: Int) = {
    new Companion(initBalance)
  }
}
```

使用类与伴生对象

```scala
//  通过伴生对象的 apply() 方法创建类的实例
val account = Companion(30)

//  在类中调用伴生对象的私有方法
assert(1 == account.id)

//  在伴生对象中访问类的私有属性
assert("Balance is 30" == Companion.getInfo(account))
```

#### 继承

单例对象不能继承另一个单例对象，但是单例对象本身可以继承另一个类，从而扩展该类的功能，实现该类的可共享的默认对象

```scala
abstract class UndoableAction(val description: String) {
  def undo

  def redo
}

object DoNothing extends UndoableAction("Do nothing") {

  override def undo: Unit = {
    println("undo")
  }

  override def redo: Unit = {
    println("redo")
  }

}
```

以上 `DoNothing` 继承了 `UndoableAction` 类，可以用于作为 `UndoableAction` 的一个默认对象来使用。

#### 应用程序对象

应用程序对象是继承类 `App` 的单例对象，该对象是一个应用程序的入口，可以通过 `args` 获得命令行参数。即使用了应用程序对象后你就不需要显示声明 `main()` 方法了，应用程序对象中定义的所有语句都会在 `main()` 方法中执行。

```scala
object Hello extends App {

  println("Hello World")

  if (args.length > 0) {
    println("The passed arguments are " + args)
  } else {
    println("There is no argument passed.")
  }
}
```


## Kotlin 篇

### Object

Kotlin 中的 Object 用法类似 Scala，但与 Scala 中的 Object 的最大区别是 Kotlin 不允许 Object 和类同名。

#### 单例对象

```kotlin
object Singleton {
    private var num = 0

    fun sequence(): Int {
        num += 1
        return num
    }
}
```

同 Scala 一样，Kotlin 也通过 Object 来实现静态方法的功能。

例

```kotlin
assert(1 == Singleton.sequence())
assert(2 == Singleton.sequence())
assert(3 == Singleton.sequence())
```

#### 伴生对象

伴生对象的概念与 Scala 基本一致，但是 Kotlin 的伴生对象是通过关键字 `companion object` 定义在类中，而不是类外，伴生对象不与类同名。

创建类和伴生对象

```kotlin
class Companion(private var balance: Int = 0) {
    //  Access private method of companion object
    val id = Companion.sequence()

    companion object Factory {
        private var num = 0

        fun getInfo(account: Companion): String {
            return "Balance is " + account.balance
        }

        fun create(initBalance: Int): Companion {
            return Companion(initBalance)
        }

        private fun sequence(): Int {
            num += 1
            return num
        }
    }
}
```

使用类与伴生对象

```kotlin
//  通过伴生对象的方法创建类的实例
val account = Companion.create(30)

//  在类中调用伴生对象的私有方法
assert(1 == account.id)

//  在伴生对象中访问类的私有属性
assert("Balance is 30" == Companion.getInfo(account))
```

#### object 表达式

object 表达式用于扩展类的功能，使用时类似 Java 中的匿名内部类。

定义一个类和接口

```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B {
    fun info()
}
```

使用 object 表达式扩展类和接口

```kotlin
val ab = object : A(1), B {
    override fun info() {
        println("info")
    }

    override val y: Int
        get() = 15
}
println(ab.y)
println(ab.javaClass)
```

以上示例本质上是生成了一个实现类和接口的匿名内部类的对象。在 Kotlin 中也可以不指定任何父类，直接产生匿名内部类：

```kotlin
val obj = object {
    val x = 10
    val y = 20
}
println(obj.x)
println(obj.javaClass)
```


## 总结

- Scala 和 Kotlin 使用单例对象来提供其它语言中的全局常量和静态方法的特性
- Scala 的伴生对象与类同名且平级，Kotlin 的伴生对象定义在类中且不同名
- Kotlin 的 object 表达式可以实现简单的继承功能

---

项目源码见 [JGSK/_18_object](https://github.com/SidneyXu/JGSK)

