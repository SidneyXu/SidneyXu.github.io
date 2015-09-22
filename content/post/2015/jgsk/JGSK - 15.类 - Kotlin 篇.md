---
comments: true
date: 2015-09-22T00:31:45+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JSGK-15-class-kotlin"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 15.类 - Kotlin 篇
toc: true
topics:
- JGSK
---

## 定义一个类

Kotlin 同 Scala 一样使用关键字 `class` 来定义类，同时类的属性必须明确指明初始值

```kotlin
class Person {
    //  属性
    var age = 0
    //  行为
    fun say(message: String) {
      println(message)
    }
}
```

<!--more-->

新建一个类的实例无需 `new` 关键字

```kotlin
val person = Person()
```

>Kotlin 文件中可以定义多个类

## Setter 与 Getter

与 Scala 一样提供了默认 Setter 和 Getter 方法，且默认的 Setter 方法名为 `set()`，Getter 方法名为 `get()`。当你直接使用属性时其实就是调用这些方法。

例

```kotlin
val person = Person()
//  修改 age
person.age = 10

//  读取 age
println(person.age)
```

Kotlin 中自定义 Setter 和 Getter 与其它语言都不一样，需要紧跟着属性名声明 `set()` 与 `get()`

例

```kotlin
class Person {

    var trueAge: Int
        get() = age
        set(pAge) {
            if (pAge > age) {
                age = pAge
            }
        }
}
```

调用自定义的 Setter 和 Getter

```kotlin
person.trueAge = 20
person.trueAge = 14
println(person.trueAge) //20
```

如果一个属性被声明为值的话，则 Kotlin 只会为其提供默认的 Getter 方法，而没有 Setter 方法。

例

```kotlin
val birthday = Date()
```

你也可以仅仅自定义 Setter 和 Getter 的访问权限而不是实现

```kotlin
var setterVisiblity: String = "foo"
    private set
```

## Backing 域

由于调用属性就是调用方法，所以如果上节改写为如下形式的话会陷入自己调用自己的无限循环中

```kotlin
var age: Int
    get() = age
    set(pAge) {
        if (pAge > age) {
            age = pAge
        }
    }
```

为此，Kotlin 提供了 Backing 域来直接调用属性而不是方法，Backing 声明时只要添加符号 `$` 到指定的属性前就可以了。

例

```kotlin
var backAge: Int = 0
    get() = $backAge
    set(pAge) {
        if (pAge > $backAge) {
            $backAge = pAge
        }
    }
```


## 构造方法

Kotlin 中构造方法也分为主构造方法和副构造方法，只不过 Scala 中称为 "Main Constructor" 和 "Slave Constructor"，而 Kotlin 中称为 "Primary Constructor" 和 "Secondary Constructor"。

### 主构造方法

主构造方法紧跟在类的声明之后

```kotlin
class Man(val name: String, var age: Int){}
```

主构造方法的参数可以声明为 `val` 或 `var` ，使用方法与其声明为成员变量时相同。

此外，类中可以声明 `init` 语句块，该语句块中的所有可执行语句都属于主构造器，在对象被创建时都会被调用。 所以以上类可以改写为

```kotlin
class Man(val name: String, var age: Int){
    init {
        println("Sentences in primary constructor")
    }
}
```

每创建一个 `Man` 的实例，语句 "Sentences in Main Constructor" 都会被打印。

### 副构造方法

副构造方法使用 `constructor()` 声明。所有副构造方法都必须调用主构造方法或其它副构造方法。

例

```kotlin
//  主构造方法
class Man(val name: String, var age: Int) {

    //  副构造方法
    //  调用主构造方法
    constructor(name: String) : this(name, 0) {
    }

    //  调用主构造方法
    constructor(age: Int) : this("Default Name", age) {
    }

    //  调用其它副构造方法 
    constructor() : this("Default Name") {
    }
}
```

创建实例时可以使用主构造方法也可以使用副构造方法

```kotlin
val fred = Man("Fred", 21) // Primary Constructor
val peter = Man("Fred") //  Secondary Constructor
val jack = Man(21) //  Secondary Constructor
```

### 深入构造方法

在使用构造方法时我们也可以为每个参数指定默认值

```kotlin
class Man(val name: String = "Default Name",
          var age: Int = 0){}
```

在声明构造方法的参数时我们也可以像在类中添加成员一样添加限定符

```kotlin
class Man(val name: String,
          var age: Int,
          private var from: String = "USA") {}
```

### 私有主构造方法

我们可以为主构造方法添加关键字 `private constructor` 从而声明主构造方法为私有权限，此时类只能通过副构造方法进行创建。

```kotlin
class Woman private constructor(val name: String, val age: Int) {
    constructor(name: String) : this(name, 0) {
    }
}

//  Wrong!! 无法调用私有主构造方法
//  val mary = Woman("Mary",21)

val jane = Woman("Jane")
```

## Empty 类

Kotlin 中允许声明一个没有类体的类用于仅仅表示类型

```kotlin
class Empty
```

## 不可变对象

Kotlin 中有一些方法可以实现部分功能，但是语言本身也没有直接提供这样一种类。

---

项目源码见 [JGSK/_15_class](https://github.com/SidneyXu/JGSK)



