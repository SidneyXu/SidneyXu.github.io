---
comments: true
date: 2015-09-22T00:31:39+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JSGK-15-class-scala"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 15.类 - Scala 篇
toc: true
topics:
- JGSK
---

## 定义一个类

Scala 中也使用关键字 `class` 来定义类，但是类的属性必须明确指明初始值，而不是像 Java 和 Groovy 一样有默认值。

```scala
class Person {
    //  属性
    var age = 0
    //  行为
    def say(message: String) {
      println(message)
    }
}
```

<!--more-->

新建一个类的实例同 Java，但是如果调用无参构造方式时可以省略小括号

```scala
val person = new Person()

//  或

val person = new Person
```

Scala 类文件中可以定义多个类。

## Setter 与 Getter

与 Groovy 一样，Scala 也提供了默认 Setter 和 Getter 方法，只是这些方法的名字比较特别。Scala 中 默认 Setter 方法名为 `属性名_=`，Getter 方法名为 `属性名`。同 Groovy 一样，当你直接使用属性时其实就是调用这些方法。

例

```scala
val person = new Person
//  修改 age,实际调用的是 person.age_=(10)
person.age = 10

//  读取 age，实际调用的是 person.age()
println(person.age)
```

当然你也可以在类中自定义 Setter 和 Getter 方法，且自定义的方式与 Groovy 完全不一样。

Scala 中自定义 Setter 和 Getter 的步骤如下

- 声明属性为 `private`
- 提供修改该属性的 Setter 方法 `_=`
- 提供访问该属性的 Getter 方法

例

```scala
class Person {

  private var privateAge = 0

  def trueAge = privateAge
  def trueAge_=(pAge: Int) {
    if (pAge > privateAge) {
      privateAge = pAge
    }
  }
}
```

调用自定义的 Setter 和 Getter

```scala
person.trueAge = 20
person.trueAge = 14
println(person.trueAge) //20
```

如果一个属性被声明为值的话，则 Scala 只会为其提供默认的 Getter 方法，而没有 Setter 方法。

例

```scala
val birthday = new Date()
```

## 对象私有域

对象私有域是一个 Scala 比较特别的地方，它可以控制一个属性只能在该对象内部调用。

可能理解起来比较困难，我们先看一个一般的例子

```scala
class Person {
  var age = 0
  def isYounger(other: Person) = age < other.age
}
```

在以上代码中，`isYounger()` 方法接收类的另一个实例，并且将这个实例的 `age` 属性与当前实例的 `age` 属性进行比较。如果使用对象私有域修饰 `age` 的话则`isYounger()` 可以接收另一个实例，但无法调用传入的实例的 `age` 属性，只能调用自己 `age` 属性。

这种对象私有域在 Scala 中使用 `private[this]` 来声明

例

```scala
class Person {
  private[this] var age = 0
  //    此时以下调用方式就会报错
  //    def isYounger(other: Person) = age < other.age
}
```

## 构造方法

Scala 中构造方法分为主构造方法和副构造方法。

### 主构造方法

主构造方法紧跟在类的声明之后

```scala
class Man(val name: String, var age: Int){}
```

主构造方法的参数可以声明为 `val` 或 `var` ，使用方法与其声明为成员变量时相同。

此外，类中的所有可执行语句都属于主构造器，在对象被创建时都会被调用。 所以以上类可以改写为

```scala
class Man(val name: String, var age: Int){
    println("Sentences in Main Constructor")
}
```

每创建一个 `Man` 的实例，语句 "Sentences in Main Constructor" 都会被打印。

### 副构造方法

副构造方法使用 `this()` 声明。所有副构造方法必须在第一行调用主构造方法或其它副构造方法。

例

```scala
//  主构造方法
class Man(val name: String, var age: Int) {

  //  副构造方法
  def this(name: String) {
    //  调用主构造方法
    this(name, 0)
  }

  def this(age: Int) {
    //  调用主构造方法
    this("Default Name", age)
  }

  def this() {
    //  调用其它副构造方法 
    this("Default Name")
  }
}
```

创建实例时可以使用主构造方法也可以使用副构造方法

```scala
val fred = new Man("Fred", 21) // Main Constructor
val peter = new Man("Fred") //  Slave Constructor
val jack = new Man(21) //  Slave Constructor
```

如果声明时不添加 `val` 或 `var` 修饰符则其相当于声明了一个对象私有域。

```scala
class Man(val name: String,
          var age: Int,
          description: String) {}
```

### 深入构造方法

在使用构造方法时我们也可以为每个参数指定默认值

```scala
class Man(val name: String = "Default Name",
          var age: Int = 0){}
```

在声明构造方法的参数时我们也可以像在类中添加成员一样添加限定符

```scala
class Man(val name: String,
          var age: Int,
          private var from: String = "USA") {}
```

也可以不加任何限定符，此时就相当于定义了一个对象私有域

```scala
class Man(val name: String,
          var age: Int,
          description: String = "none") {}
```

### 私有主构造方法

我们可以为主构造方法添加关键字 `private` 从而声明主构造方法为私有权限，此时类只能通过副构造方法进行创建。

```scala
class Woman private(val name: String, val age: Int) {
  def this(name: String) {
    this(name, 0)
  }
}

//  Wrong!! 无法调用私有主构造方法
//  val mary = new Woman("Mary",21)

val jane = new Woman("Jane")
```

## Java 风格的 Bean

如果希望生成的 Bean 也能被 Java 代码使用，那么Scala 允许你生成 Java 风格的 Setter 与 Getter。只需要在指定的属性上添加注解 `BeanProperty` ，在Boolean 型属性上添加注解 `BooleanBeanProperty`。

```scala
class Model {

  @BeanProperty
  var name = ""

  @BooleanBeanProperty
  var visible = false
}

```

## 不可变对象

Scala 并没有直接提供这样一种类（尽管样本类之类的看起来有那么点像）。但是由于 Scala 本身提供了大量不可变类作为默认实现，且 Scala 官方建议总是使用 `val` 声明变量，`var` 只应该用在不得不的情况下，所以你只要遵守这些规定那么编写不可变对象也不是件难事。

---

项目源码见 [JGSK/_15_class](https://github.com/SidneyXu/JGSK)


