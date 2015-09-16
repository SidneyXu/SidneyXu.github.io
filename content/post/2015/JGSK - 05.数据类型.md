---
comments: true
date: 2015-09-16T07:00:15+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-05-datatype"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 05.数据类型
toc: true
topics:
- JGSK
---


## Java 篇

Java 的数据类型主要分为基本数据类型和引用数据类型。

### 基本类型

Java 的基本类型有 int, long, float, double, char, boolean，即以小写字母开头的数据类型。
<!--more-->

例

```java
int x = 2;
float y = 0.1f;
boolean flag = false;
```

### BigDecimal

Java 的基本数值类型及其包装类在进行浮点操作时，很有可能会损失精度。为此，Java 引进了 BigDecimal 类，可以在不损失精度的情况下进行计算。但是不幸的是 Java 的 BigDeciaml 的构造器设计得非常反人类。

```java
System.out.println(2.0 - 1.8);	//0.19999999999999996
System.out.println(new BigDecimal(2.0).subtract(new BigDecimal(1.8)));	//0.1999999999999999555910790149937383830547332763671875
System.out.println(new BigDecimal("2.0").subtract(new BigDecimal("1.8")));	//0.2
```

从上面的例子可以看到，BigDecimal 有字符串和数值两种构造器，字符串构造器构造的 BigDecimal 才能满足我们的需求，而不是直接使用数值进行构造，基本上用过这个类的人都会踩过这个坑。

### 引用数据类型

Java 中除基本数据类型之外的所有数据都属于引用数据类型，且所有引用数据类型都是 Object 类的子类。

### 类型转换

#### 隐式转换

所谓的隐式转换即无需显示指明需要进行类型转换，由编译器根据变量的类型自动推断。

Java 的隐式转换只发生在使用字面值给变量赋值或者向上转型时，如以下例子

```java
byte b = 1;
int i = b;	//	byte 类型向上转型为 int 类型
char c = 1;	//	使用 int 型的字面值 1 为 char 变量赋值
```

如上述的例子，Java 默认数值字面值为 int 型，只要该数值在 byte 取值范围内，字面值就可以直接赋值给 byte 类型变量。此外由于 int 类型取值范围比 byte 大，所以 byte 变量可以直接赋值给 int 变量，即向上转型。

#### 显式转换

无论是基本类型还是引用类型都是使用 `(dataType)value` 的语法进行转换。显示转换又称作强制转换，且向下转型时很可能会丢失精度。

```java
int i = (int) 99.98
```

#### 类型推断

Java 使用关键字 `isInstanceof` 来判断变量的类型

例：

```java
private static void bar(Object foo) {
    if (foo instanceof String) {
        String fooString = (String) foo;
        System.out.println(fooString.toUpperCase());
    }
}

Object foo = "foo";
bar(foo);
```

从以上例子可以看到，"foo" 已经通过类型判断知道是 `String` 类型了，但是还需要进行强制类型转换，并且再声明一个变量，这一做法稍显多余。稍后可以看到 Groovy 的做法要优雅得多。

## Groovy 篇

由于 Groovy 就是 Java，所以 Groovy 的数据类型也与 Java 完全一样。但是 Groovy 根据情况的不同会对数据进行包装。

```groovy
int i = 1
1.toString()
```

以上第一行在编译后的结果与 Java 完全一样，而第二行由于调用了方法，所以 Groovy 会将其转换为 Integer 类型。这一自动化操作省去了在写 Java 时的不少麻烦。

所以可以认为 Groovy 中一切皆对象。

Groovy 通常将数据类型分为静态类型和动态类型。

### 静态类型

静态类型与 Java 的声明方式完全一样，声明后就不可以再改变数据类型。

```groovy
int x = 2
float y = 0.1f
boolean flag = false
```

### 动态类型

Groovy 使用关键字 `def` 声明动态类型变量，与静态类型变量不一样，动态类型变量在定义后可以任意改变类型，类似 Javascript。

定义动态类型变量

```groovy
def dx = 2
def dy = 0.1
def dflag = false
```

改变类型

```groovy
Date staticDate = new Date()
// 静态类型变量无法改变数据类型，会报 'java.lang.Integer' to class 'java.util.Date'
//        staticDate = 2

// 可以由 Date 类型转换为 Integer 类型
def dynamicDate = new Date()
dynamicDate = 2
```

### BigDecimal

与 Java 不同，Groovy 中定义一个浮点类型数据实际就是使用 BigDecimal 的字符串构造器定义一个 BigDecimal，所以 Groovy 中不需要做任何额外操作直接就可以做精确的浮点运算。

```groovy
println(2.0 - 1.8);	//0.2
```

### 类型转换

#### 隐式转换

同 Java

#### 显式转换

Groovy 使用方法来实现基本类型的显式转换，语法看起来更加优雅

```groovy
int i = 99.98.toInteger()
"99.12".toDouble()
```

但是 Groovy 并没有提供到 "char" 类型的转换方法，所以 "char" 类型还是需要使用 Java 样式的转换方式或者使用另一种方式的类型转换：

```groovy
char c = 99 as char
```

对于引用类型的话则转换方式则类似 Java。

#### 类型推断

对于引用类型而言，Groovy 也使用关键字 `instanceof` 做类型推断，但是 Groovy 也提供了 `isXXX()` 的语法糖来进行基本类型的推断。

可以看到，通过 `instanceof` 进行判断以后，变量就可以直接调用其真实类型的方法，无需再声明另一个变量并进行强制类型转换，这一做法相比 Java 方便很多。但是如果使用的是 `isXXX()` 的话则不支持此特性。

```groovy
def static bar(foo) {
    if (foo instanceof String) {
        println(foo.toUpperCase())
    }
    if (foo instanceof Double) {
        println(foo.intValue())
    }
    if (foo.isDouble()) {
        println(foo.toDouble())
    }
}

def foo = "foo"
bar(foo)
```

## Scala 篇

Scala 中并不存在基本类型，所有数据都是对象，且都继承自 `Any` 类。Scala 类型主要分为 `AnyVal` 和 `AnyRef`，两者都是 `Any` 的子类。

### AnyVal

AnyVal 分为 Int, Long, Float, Double, Boolean, Char, Byte，Unit 几种类型。除了最后一种，其它可以看做是 Java 上的基本类型的包装类。而 Unit 则相对于 Java 平台上的 `Void`，即表示没有返回值。

```scala
var x: Int = 2
var y: Float = 0.1f
var flag: Boolean = false
```

### AnyRef

AnyRef 是 Scala 中所有引用类的基类，本质上就是 Java 中的 Object 类。

### BigDecimal

Scala 中的 BigDecimal 虽然没有 Groovy 那么方便，但是也不像 Java 那样反人类。

```scala
println(BigDecimal(2) - BigDecimal(1.8))
```

### 类型转换

#### 隐式转换

类似 Java

#### 显式转换

对于 AnyVal 类型，Scala 使用方法进行类型转换

```scala
var i:Int = 99.98.toInt
"99.12".toDouble
```

对于 AnyRef类型，Scala 则使用方法 `asInstanceOf()` 类进行类型转换

```scala
val fooString: String = foo.asInstanceOf[String]
```

#### 类型推断

Scala 使用 `isInstanceOf` 来进行类型推断，整个过程与 Java 基本一样，需要额外定义变量并显式进行类型转换。

```scala
def bar(foo: Any): Unit = {
  if (foo.isInstanceOf[String]) {
    val fooString: String = foo.asInstanceOf[String]
    println(fooString.toUpperCase)
  }
}
```


## Kotlin 篇

Kotlin 中一切皆对象，`Any` 为所有类的基类。

### 数据类型

Kotlin 中的所有类型都是引用类型，但是与其它几门语言不一样，Kotlin 中的 Char 类型不属于数值类型。

```kotlin
var x: Int = 2
var y: Float = 0.1f
var flag: Boolean = false

val c: Char = 1	//  错误，Char 不是数值类型
```

### BigDecimal

Kotlin 并没有专门的 BigDecimal 类，需要调用 Java 来完成计算

```kotlin
println(BigDecimal("2").subtract(BigDecimal("1.8")));
```

### 类型转换

#### 隐式转换

Kotlin 只支持使用字面值给变量赋值时的隐式转换，不支持自动向上转型，这点上比其它几门语言都要严格不少。

```kotlin
var b: Byte = 1

//  错误，不支持自动向上转型
val i: Int = b
```

#### 显式转换

Kotlin 也使用方法进行内置类型的转换

```kotlin
val i:Int = 99.98.toInt()
"99.12".toDouble()
```

对于其它类型的数据，Kotlin 使用 `as` 关键字进行类型转换。

```kotlin
val fooString: String = foo as String
```

#### 类型推断

Kotlin 使用关键字 "is" 来进行类型判断，且在类型判断后数据会被隐式转换为真正的类型，所以可以直接调用该类型的所有方法而无需手动强制转换，Kotlin 称这一过程为 "Smart Cast"。

```kotlin
fun bar(foo: Any) {
    if (foo is String) {
        println(foo.toUpperCase())
    }
}

val foo = "foo"
bar(foo)
```

## 总结

- 除了 Java 之外，其它语言都含有一切皆对象这一概念
- 每种语言都有自己的基类：Java->Object, Grooy->Object, Scala->Any, Kotlin->Any
- 除了 Java 之外，其它语言都使用方法来完成内置类型的强制类型转换

---

项目源码见 [JGSK/_05_datatype](https://github.com/SidneyXu/JGSK)
