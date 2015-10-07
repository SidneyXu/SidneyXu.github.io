---
comments: true
date: 2015-09-29T09:54:14+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-23-trait"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 23.Trait
toc: true
topics:
- JGSK
---

## 共通篇

Trait 中文名为特质。特质是字段和行为的集合，可以拥有抽象成员也可以拥有普通成员。特质可以看做是一种特殊形式的接口，但是特质主要用于实现多重继承。

多重继承虽然便利，但是会带来 Diamond Problem，即 B 和 C 都实现了 A 的某个方法，而 D 继承了 B 和 C 但是没有重写该方法，此时调用 D 持有的该方法到底来自于 B 还是 C。因此过分使用特质会让程序本身难以理解。

<!--more-->

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/Diamond_inheritance.svg/440px-Diamond_inheritance.svg.png)

## Java 篇

Java 诞生时就倡导语法本身应该简单容易学习，所以设计时 Java 就只支持单继承，因此 Java 并不支持特质，不过可以通过内部类实现类似多重继承的功能。

## Groovy 篇

### 创建特质

特质使用关键字 `trait` 声明，可以拥有普通成员和抽象成员。

```groovy
trait MessageHandler {
    //  属性
    int minLenght

    //  方法
    //  普通方法
    void echo(String msg) {
        println(msg)
    }

    //  抽象方法
    abstract void show(String msg)
}
```

>Groovy 中特质本质上是运行时对接口的实现，所以其方法的访问控制符只支持 `public` 和 `private`。

### 使用特质

特质就像接口一样使用关键字 `implements` 进行实现。

```groovy
class DefaultMessageHandler implements MessageHandler {

    @Override
    void show(String msg) {
        println(msg)
    }
}

def handler = new DefaultMessageHandler()
handler.show("foo")
```

### 实现接口与特质

特质可以实现接口。

```groovy
interface Named {
    String name()
}
trait Greetable implements Named {
    String greeting() { "Hello, ${name()}!" }
}
```

特质也可以实现其它特质。

```groovy
trait OutputLogger implements Logger{
    @Override
    void log(String msg) {
        super.log(msg)
    }
}
```

多重继承时使用 `,` 作为分隔符，这点和接口一样

```groovy
class Duck implements FlyingAbility, SpeakingAbility {}
```

>特质实现接口和特质使用的是关键字 `implements`，而接口实现接口使用的则是关键字 `extends`。

### 带有特质的对象

使用 `withTraits` 可以动态通过对象实现特质而不用在类上定义特质。

```groovy
def logger2 = new BasicLogger().withTraits(OutputLogger)
logger2.log("hello world")
```

`withTraits` 中可以包含多个特质，每个特质以 `,` 分隔。

### Diamond Problems

对于 Diamond Problems，Groovy 采用的是最右边定义的赢的规则。即一个类实现了多个包含同名方法的特质时，总是调用最右边的特质定义的方法。

定义特质 A,B 和实现特质的类 C

```groovy
trait A {
    void echo() {
        println("A")
    }
}

trait B {
    void echo() {
        println("B")
    }
}

class C {}
```

调用 C

```groovy
def c1 = new C().withTraits(A, B)
c1.echo()   //  B
def c2 = new C().withTraits(B, A)
c2.echo()   //  A
```

### 链式操作

链式操作即实现多个特质时，每个特质可以通过 `super` 调用上一个特质的实现。特质的链式操作可以说是一个比较复杂的概念，建议使用 Debug 进行跟踪来进行理解。简单来说就是基于 "最右为赢" 的原则，总是调用最右边的特质，在该特质调用 `super` 后就调用其左边的特质的实现。

以下是一个链式操作的完整的例子

定义一个 Logger 接口

```groovy
interface Logger {
    void log(String msg)
}
```

>链式操作时最上层定义的必须为接口，而不能是拥有抽象类的特质 `trait Logger {abstract void log(String msg}`。

定义实现该接口的几个特质

```groovy
trait OutputLogger implements Logger {

    @Override
    void log(String msg) {
        println("--> Seeing msg in OutputLogger.")
        println(msg)
    }
}

trait ShortLogger implements Logger {

    final int maxLength = 15

    @Override
    void log(String msg) {
        println("--> Seeing msg in ShortLogger.")
        if (msg.length() <= maxLength)
            super.log(msg)
        else
            super.log(msg.substring(0, maxLength - 3) + "...")

    }
}

trait TimeStampLogger implements Logger {
    @Override
    void log(String msg) {
        println("--> Seeing msg in TimeStampLogger.")
        super.log(new Date().toString() + " " + msg)
    }

}
```

以上定义了三个特质，其中 `OutputLogger` 用于执行打印操作，`ShortLogger` 限制传入的消息长度为15，`TimeStampLogger` 用于在消息前追加当前时间。

执行链式操作

```groovy
public class BasicLogger {
}

def loggerX = new BasicLogger().withTraits(OutputLogger, TimeStampLogger, ShortLogger)
loggerX.log("hello world loggerX")  //  Mon Oct 05 12:01:49 CST 2015 hello world ...

def loggerY = new BasicLogger().withTraits(OutputLogger, ShortLogger, TimeStampLogger)
loggerY.log("hello world loggerY")  //  Mon Oct 05 1...
```

以上 loggerX 先进行了截取操作再追加时间，而 loggerY 先追加时间再进行截取。




## Scala 篇

### 创建特质

Scala 中特质也使用关键字 `trait` 声明，可以拥有普通成员和抽象成员。

```scala
trait MessageHandler {
    //  属性
    val minLength: Int

    //  方法
    //  普通方法
    def echo(msg: String) {
        println(msg)
    }

    //  抽象方法
    def show(msg: String)
}
```

### 使用特质

Scala 中只实现一个特质时使用关键字 `extends` 。

```scala
class DefaultMessageHandler extends MessageHandler {
    override val minLength: Int = 100
    override def show(msg: String): Unit = {
        println(msg)
    }
}

val handler = new DefaultMessageHandler
handler.show("foo")
```

### 实现特质

一个特质可以实现其它特质。

```scala
trait OutputLogger extends Logger {
    override def log(msg: String): Unit = println(msg)
}
```

实现多个特质时使用 `with` 作为分隔符

```scala
class Duck extends FlyingAbility with SpeakingAbility {}
```

### 带有特质的对象

Scala 中也可以使用关键字 `with` 来动态通过对象实现特质而不用在类上定义特质。

```scala
val logger = new BasicLogger with OutputLogger
logger.log("hello world")
```

### Diamond Problems

对于 Diamond Problems，Scala 采用的方式非常简单，类必须重写造成 Diamond Problems 的方法，如果不重写的会报运行时异常 "Inherits conflicting members"。

### 特质的构造顺序

Scala 中特质构造顺序遵循以下原则

- 首先调用超类的构造器
- 特质构造器在超类构造器之后，类构造器之前执行
- 特质由左至右被构造
- 每个特质中，父特质先被构造
- 如果多个特质有同一个父特质，且父特质已被构造了，则不会被再次构造
- 所有特质构造完毕，子类才被构造

例

有如下类

```scala
trait Logger
trait FileLogger extends Logger
trait ShortLogger extends Logger
class Account
class SavingAccount extends Account with FileLogger with ShortLogger
```

则 SavingAccount 的构造顺序为

1. Account
2. Logger
3. FileLogger
4. ShortLogger
5. SavingAccount

### 链式操作

Groovy 中虽然没有 Groovy 的最右为赢的原则，但是同样的 Scala 中右边的特质调用 `super` 后会调用其左边的特质的实现。

以下是一个链式操作的完整的例子

定义一个 Logger 接口

``` scala
trait Logger {
    def log(msg: String)
}
```

注意 Groovy 这里使用的是接口，而 Scala 为特质

定义实现该接口的几个特质

```scala
trait OutputLogger extends Logger {
    override def log(msg: String): Unit = println(msg)
}

trait ShortLogger extends Logger {
    val maxLength = 15

    override def log(msg: String): Unit = {
        super.log(
            if (msg.length <= maxLength) msg else msg.substring(0, maxLength - 3) + "..."
        )
    }
}

trait TimeStampLogger extends Logger {
    override def log(msg: String): Unit = {
        super.log(new Date() + " " + msg)
    }
}
```

以上定义了三个特质，其中 `OutputLogger` 用于执行打印操作，`ShortLogger` 限制传入的消息长度为15，`TimeStampLogger` 用于在消息前追加当前时间。

执行链式操作

```scala
class BasicLogger {
}

val loggerX = new BasicLogger with OutputLogger with TimeStampLogger with ShortLogger
loggerX.log("hello world loggerX") //   Mon Feb 16 11:46:06 CST 2015 hello world ...
val loggerY = new BasicLogger with OutputLogger with ShortLogger with TimeStampLogger
loggerY.log("hello world loggerY") //   Mon Feb 16 1...
```

以上 loggerX 先进行了截取操作再追加时间，而 loggerY 先追加时间再进行截取。

### 什么时候应该使用特质而不是抽象类？

如果你想定义一个类似接口的类型，你可能会在特质和抽象类之间难以取舍。这两种形式都可以让你定义一个类型的一些行为，并要求继承者定义一些其他行为。一些经验法则：

- 优先使用特质。一个类扩展多个特质是很方便的，但却只能扩展一个抽象类。
- 如果你需要构造函数参数，使用抽象类。因为抽象类可以定义带参数的构造函数，而特质不行。
- 如果需要使用的类是从 Java类继承过来的，使用抽象类。
- 如果需要考虑效率问题，使用抽象类。Java的动态绑定机制决定了直接方法要快于接口方法。而特质最终是被编译成接口。


## Kotlin 篇

Kotlin 曾经支持过特质，不过后来由于多重继承等特性会把程序搞得过于复杂，而且也不是没有其它解决方案，所以 Kotlin 目前的版本已经将特质废弃了。




## 总结

- 特质最终会被编译成接口
- 只有 Groovy 和 Scala 支持特质
- 对于 Diamond 问题，Groovy 的原则是最右为赢，Scala 的原则是必须重写


---

项目源码见 [JGSK/_23_trait](https://github.com/SidneyXu/JGSK)