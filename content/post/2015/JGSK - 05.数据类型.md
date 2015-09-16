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
- Dev
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
Object foo = "foo";
if (foo instanceof String) {
    String fooString = (String) foo;
    System.out.println(fooString.toUpperCase());
}
```

从以上例子可以看到，"foo" 已经通过类型判断知道是 `String` 类型了，但是还需要进行强制类型转换，并且再声明一个变量，这一做法稍显多余。稍后可以看到 Kotlin 的做法要优雅得多。

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

与 Java 不同，Groovy 中定义一个浮点类型就是使用 BigDecimal 的字符串构造器，所以 Groovy 中不需要做任何额外操作直接就可以做精确的浮点运算。

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

//TODO
但是 Groovy 并没有提供到 "char" 类型的转换方法，所以 "char" 类型还是需要使用 Java 样式的转换方式。

对于引用类型的话则类似 Java。

#### 类型推断

同 Java

## Scala

Scala 中一切皆对象。Scala类型分为AnyVal 和 AnyRef，两者都是 Any 的子类。

### AnyVal

AnyVal 有点像 Java 的基本类型的包装类，有 Int, Long, Float, Double, Boolean, Char, Byte 几种。

```scala
var x: Int = 2
var y: Float = 0.1f
var flag: Boolean = false
```

### AnyRef

AnyRef 类似 Java 中除基本类型的包装类以外的所有引用类型。

### BigDecimal

Scala 中的 BigDecimal 虽然没有 Groovy 那么方便，但是不会像 Java 那样产生迷惑

```scala
println(BigDecimal(2) - BigDecimal(1.8))
```

### 类型转换

#### 隐式转换

类似 Java

#### 显式转换

类似 Groovy，使用方法进行AnyVal的显式转换

```scala
var i:Int = 99.98.toInt
"99.12".toDouble
```

对于 AnyRef类型，则使用方法 `asInstanceOf()`

```scala
val fooString: String = foo.asInstanceOf[String]
```

#### 类型推断

Scala 使用 `isInstanceOf` 来进行类型推断

```scala
if (foo.isInstanceOf[String]) {
  val fooString: String = foo.asInstanceOf[String]
}
```


## Kotlin

Kotlin 中一切皆对象。

### 数据类型

Kotlin 中的所有类型都是引用类型，但是其中的 Char 类型不属于数值类型

```kotlin
var x: Int = 2
var y: Float = 0.1f
var flag: Boolean = false

val c: Char = 1	//错误，Char 不是数值类型
```

### BigDecimal

Kotlin 并没有专门的 BigDecimal 类，需要使用 Java 一样的方法

```kotlin
println(BigDecimal("2").subtract(BigDecimal("1.8")));
```

### 类型转换

#### 隐式转换

Kotlin 只支持使用字面值给变量赋值时的隐式转换，不支持自动向上转型

```kotlin
var b: Byte = 1

//错误，不支持自动向上转型
val i: Int = b
```

#### 显式转换

类似 Scala 的写法

```kotlin
val i:Int = 99.98.toInt()
"99.12".toDouble()
```

#### 类型推断

Kotlin 使用关键字 "is" 来进行类型判断

```kotlin
val foo = "foo";
if (foo is String) {
    val fooString: String = foo as String
}
```
