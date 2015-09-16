---
comments: true
date: 2015-09-16T07:07:41+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-06-variable"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 06.变量与常量
toc: true
topics:
- JGSK
---

## Java 篇

### 变量

语法

```
数据类型 变量名 = 变量值；
```

例

```java
String variable = "foo";
```

<!--more-->
### 常量

在变量前加上关键字 `final` 就可以将变量转换为常量，常量的值定义后不可用进行改变。

例

```java
final String constant = "foo";
```

## Groovy 篇

### 变量

像上节所说，Groovy 中声明静态类型的变量的方式与 Java 完全一样，且静态类型定义后其数据类型就不可再被改变。

```groovy
//  第二行报错，因为 10 无法转换为 Date 类型
Date date = new Date()
date = 10

//  正常运行，因为 Date 对象可以通过 toString() 方法转换为 String 类型
String str = "10"
str = new Date()
```

以上代码实际会被编译成如下的 Java 代码

```java
Date date = (Date)ScriptBytecodeAdapter.castToType(var1[1].callConstructor(Date.class), Date.class);

String str = "10";
Object var6 = var1[2].callConstructor(Date.class);
str = (String)ShortTypeHandling.castToString(var6);
var1[3].callStatic(App.class, str);
```

可以看到虽然在源代码层面上都是使用变量 `str`，但是在编译后实际使用了 `str`, `var6` 两个变量。

定义动态变量的语法

```groovy
def 变量名 = 变量值
```

例

```groovy
def foo = "foo"
```

动态类型可以自由变换变量类型。

### 常量

Groovy 定义常量的方式与 Java 相同，但是目前的 v2.4 版本还是存在历史遗留问题，常量可以被任意修改。

例如定义如下常量

```groovy
final String constant = "foo"
```

`foo` 的值可以被任意修改，查看其编译结果

```java
String constant = "foo";
```

可以看到 `final` 完全被丢弃了，目前这个 Bug 官方声称已经在 `2.5.0-beta-1` 版本修复了，具体情况可以查看 [Inconsistent checking of final](https://issues.apache.org/jira/browse/GROOVY-1628) 和 [final keyword does not work](https://issues.apache.org/jira/browse/GROOVY-2752)。

## Scala 篇

### 变量

语法

```
var 变量名: 变量类型 = 变量值
```

例

```scala
var variable: String = "foo"
```

可以看到 Scala 的声明方式类似 UML 图，与 Java 正好相反。

### 常量（值）

在 Scala 中常量被称为值。

语法

```
val 值名: 值类型 = 具体值
```

例

```scala
val constant = "foo"
```

### 另一种声明方式

在 Scala 中，无论是声明变量还是值都可以省略类型说明，如

```scala
var variable = "foo"
val constant = "foo"
```

这种声明方式看起来像 Groovy 声明动态类型。但实际上 Scala 是比 Java 更强的静态类型语言，只是 Scala 更智能，如果你不明确指定类型的话，Scala 会通过值本身来推断其数据类型，试图改变数据类型都会报错。

## Kotlin 篇

Kotlin 在声明变量和值时与 Scala 几乎一样。

## 总结

## 总结

- Groovy 声明方式类似 Java，但是支持声明动态类型；Scala 与 Kotlin 声明方法一样。
- 虽然四种语言都支持值不变的特性，但目前 Groovy 版本存在着 Bug

---

项目源码见 [JGSK/_06_variable](https://github.com/SidneyXu/JGSK)

