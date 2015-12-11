---
comments: true
date: 2015-09-18T06:42:11+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-14-map-and-turple"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 14.映射与元祖
toc: true
topics:
- JGSK
---

## 共通篇

映射是由键值对组成的一组数据的集合。映射通常基于哈希表，最常见的实现就是 HashMap。

元组类似列表，也是表示一组数据，但是元组中的数据通常都是不同类型或者数据间表示不同含义。且元组通常只用于传递数据，所以不能进行修改。
<!--more-->

## Java 篇

### 映射

#### 建立一个 Map

```java
Map<String, Object> map = new HashMap<String, Object>();
```

同 List 一样，Java 1.7 后可以使用 Diamond 语法写成以下形式

```java
Map<String, Object> map = new HashMap<>();
```

同时 `Collections` 也提供了一个无法进行任何写操作的空映射

```java
Map<String, Integer> emptyMap = Collections.emptyMap();
//  试图调用以下方法会报 UnsupportedOperationException
//        emptyMap.put("x", 1);
```

#### 修改 Map

Put

在映射中，与映射中已有键相同表示修改，与已有键完全不相同表示添加

```java
map.put("name", "Peter");
```

Delete

```java
map.remove("age");
```

Get

```java
String name = (String) map.get("name");  
```

在 Java 1.8 中提供了如果指定的键不存在则返回默认值的接口

```java
int height = (int) map.getOrDefault("height", 170);
```

#### 其它操作

获得长度

```java
System.out.println(map.size());
```

遍历映射

```java
for (Map.Entry<String, Object> entry : map.entrySet()) {
    System.out.println("Traverse " + entry.getKey() + " is " + entry.getValue());
}
```

### 元组

Java 不支持元组

## Groovy 篇


### 映射

#### 建立一个 Map

创建空 Map

```groovy
def emptyMap = [:]
```

创建时指定内容

```groovy
def map = [name: "Peter", "age": 12, "national": "USA"]
```

Groovy 中的 Map 默认是使用 `LinkedHashMap` 实现的。

#### 修改 Map

Put

Groovy 有三种方式可以修改 Map

```groovy
map.put("name", "Jane")
map.age = 20
map["sex"] = "Female"
```

Delete

```groovy
map.remove("age");
```

Get

同 Put 一样，Groovy 有三种方式可以获取键值

```groovy
assert map.get("name") == "Jane"
assert map["name"] == "Jane"
assert map.name == "Jane"
```

或者可以使用以下方法在指定的键不存在时返回默认值

```groovy
assert map.getOrDefault("height", 170) == 170
```

#### 其它操作

获得长度

```groovy
println(map.size())
```

遍历映射

```groovy
for (entry in map) {
    println("Traverse ${entry.key} is ${entry.value}")
}
```


### 元组

创建元组

```groovy
def t1 = new Tuple()
def t2 = new Tuple(1, 2, 3)
def t3 = new Tuple(1, "abc", [10, 20, 30], [name: "Peter", "age": 12])
```

元组创建后就不支持修改操作

获取数据

元组访问时基于索引，且索引可以为负值

```groovy
assert t3.get(0) == 1
assert t3[1] == "abc"
assert t3[-1]["name"] == "Peter"
```


## Scala 篇

### 映射

同列表一样，Scala 支持不可变映射和可变映射，且不可变映射为默认实现

#### 不可变映射

##### 创建一个 Map

创建空 Map

```scala
val empty = Map()
```

创建时指定内容

```scala
val immutableMap = Map("name" -> "Peter", "age" -> 12, "national" -> "USA")
```

##### 获取值

```scala
println(immutableMap.get("name").get)
```

或者调用以下方法在键不存在时返回默认值

```scala
println(immutableMap.getOrElse("height", 170))
```

注意第一种方法 `get(key)` 返回的是一个 `Option` 对象而不是值本身，需要再调用 `get` 才能获得值。

#### 可变映射

##### 创建一个 Map

```scala
val mutableMap = mutable.HashMap("name" -> "Peter", "age" -> 12, "national" -> "USA")
```

#### 修改 Map

Put

Scala 有两种种方式可以修改 Map

```scala
mutableMap.put("name", "Jane")
mutableMap("age") = 20
```

或者通过 `+=` 根据现有映射返回新映射

```scala
mutableMap += ("id" -> 10)
```

Delete

```scala
map.remove("age")
```

Get

同不可变映射

```scala
println(mutableMap.get("name").get)
println(mutableMap.getOrElse("height", 170))
```

#### 其它操作

获得长度

```scala
println(mutableMap.size)
```

遍历映射

```scala
for ((k, v) <- mutableMap) {
  println(s"Traverse $k is $v")
}
```


### 元组

创建元组

```scala
val t1 = ()
val t2 = (1, 2, 3)
val t3 = (1, "abc", List(10, 20, 30), Map("name" -> "Peter", "age" -> 12))
```

元组创建后就不支持修改操作

获取数据

元组访问时使用属性 `_n` 

```scala
println(t3._1)
println(t3._2)
```



## Kotlin 篇

### 映射

同Scala 一样，Kotlin 支持不可变映射和可变映射，且不可变映射为默认实现

#### 不可变映射

##### 创建一个 Map

创建空 Map

```kotlin
val empty = emptyMap<String, String>()
```

创建时指定内容

```kotlin
val immutableMap = mapOf("name" to "Peter", "age" to 12, "national" to "USA")
```

Kotlin 中的不可变映射是通过 Java 的 `java.util.LinkedHashMap` 类来实现的。

##### 获取值

```kotlin
println(immutableMap.get("name"))
println(immutableMap["name"])
```

或者调用以下方法在键不存在时返回默认值

```kotlin
println(immutableMap.getOrElse("height") { 170 })
```

注意第二个方法的第二个参数为闭包，在之后会谈到

#### 可变映射

##### 创建一个 Map

```kotlin
val mutableMap = hashMapOf("name" to "Peter", "age" to 12, "national" to "USA")
```

Kotlin 中可变映射是使用 `java.util.HashMap` 等类来实现的

#### 修改 Map

Put

Kotlin 有两种种方式可以修改 Map

```kotlin
mutableMap.put("name", "Jane")
mutableMap["age"] = 20
```

Delete

```kotlin
mutableMap.remove("age")
```

Get

同不可变映射

```kotlin
println(mutableMap.get("name"))
println(mutableMap["name"])
println(mutableMap.getOrElse("height") { 170 })
```

#### 其它操作

获得长度

```kotlin
println(mutableMap.size)
```

遍历映射

```kotlin
for ((k, v) in mutableMap) {
    println("Traverse $k is $v")
}
```


### 元组

Kotlin 曾经支持元组，不过后来整个功能都被溢移除了，因为 Kotlin 开发团队认为 Tuple 的作用并不大，具体讨论可以见 [Dropping tuples](http://kotlin-developers.1258.x6.nabble.com/Dropping-tuples-td4999859.html) 和 [Migrating Tuples](http://blog.jetbrains.com/kotlin/migrating-tuples/)。


## 总结

- Groovy 和 Scala 支持元组
- Scala 与 Kotlin 默认映射为不可变形式
- Java 1.8 及其它语言都支持 `getOrElse` 操作
- Scala 从 Map 中直接取出的为 `Option` 对象而不是值本身

---

项目源码见 [JGSK/_14_map](https://github.com/SidneyXu/JGSK)

