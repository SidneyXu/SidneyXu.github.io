---
comments: true
date: 2015-09-17T07:55:16+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-09-null"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 09.Null
toc: true
topics:
- JGSK
---

## Java 篇

### null

Java 使用关键字 `null` 表示空值，即没有任何引用。使用 `==` 来判断一个值是否是空值。

```java
Integer x = null;
System.out.println(x == null);
```

<!--more-->

### NullPointerException

Java 在 1.8 之前对于空指针异常没有什么比较好的处理方式，你必须在每个可能为空值的地方手动进行 `if-else` 的判断或者使用 Google 的 `guava` 的 `Option` 类。Java 在 1.8 版本在语言层面引入了 `Optional` 类从而可以不需要第三包就能实现同样的功能。

传统的方式

```java
int result;
if (x != null) {
    result = 3 + x;
} else {
    result = 0;
}
```

Java 1.8

```java
Optional<Integer> nullableX = Optional.ofNullable(x);
result = 3 + nullableX.orElseGet(new Supplier<Integer>() {
    public Integer get() {
        return 0;
    }
});
```

也可以将 Optional 用于集合中

传统方式

```java
List<Person> persons = new ArrayList<Person>() {{
    add(null);
    add(new Person("Peter"));
}};
for (Person p : persons) {
    if (p != null) {
        p.getName();
    }
}
```

Java 1.8

```java
List<Optional<Person>> opPersons = new ArrayList<Optional<Person>>() {{
    Person p = null;
    add(Optional.ofNullable(p));
    add(Optional.ofNullable(new Person("Peter")));
}};

for (Optional<Person> p : opPersons) {
    if (p.isPresent()) {
        System.out.println(p.get().getName());
    }
}
```

使用 Optional 可以很好的解决空指针异常的问题，不过其语法本身在有些情况却未必比 `if` 判断要优美多少。

## Groovy 篇

### null

Groovy 也使用关键字 `null` 表示空值。可以使用 `==` 或 `is()` 来判断一个值是否是空值。

```java
Integer x = null
println(x == null)
println(x.is(null))
```

### NullPointerException

Groovy 提供了安全操作符 `?.` 来避免调用方法时产生的空指针异常，在其他场合（如Java 篇 的 x + 3）则只能使用传统的解决方式或者上一章节提到的猫王操作符。

```groovy
List<Person> persons = [null, new Person("Peter")]
for (Person p : persons) {
    println(p?.name)
}
```

## Scala 篇

### null

Scala 表示无的概念非常复杂，具体本篇最后一节再讲。其中最常见的空值也是使用 `null` 表示，使用 `==` 进行判断。

```scala
val x = null
println(x == null)
```

### NullPointerException

Scala 内置了 `Option` 类，使用方法类似 Java 1.8 才提供的 `Optional`类。

```scala
val persons: List[Option[Person]] = List(None, Option(new Person("Peter")))
for (p <- persons) {
  if (p isDefined) {
    println(p.get.name)
  }
}
```

实际上当建立 Option 对象时，建立的是其子类 `Some` 或者 `None`，一个表示有值，一个表示空值。

### 无

Scala 有以下几种表示无的方式，可以说数量多得让人恼火

- *Null* 一个 Trait，trait 是什么之后的章节再说，现在先理解为类
- *null* Null 的实例，由于 Scala 中一切皆对象，所有空值本身也是对象
- *Nil*  空列表，表示列表本身虽然存在但是没有任何内容
- *Nothing* 一个 Trait，是 Scala 中所有类的子类
- *None* Option 的子类，用于表示返回值为空
- *Unit* 表示没有返回值

## Kotlin 篇

### null

Kotlin 也使用 `null` 表示空值，对于一般对象使用 `==` 进行空值的判断，对于字符串还提供了 `isNullOrEmpty()` 和 `isNullOrBlank()` 两个方法。

```kotlin
var x = null
println(x == null)

//  判断是否为 null 或空字符串
println(x.isNullOrEmpty())

//  判断是否为 null 或空字符串或只包含 whitespace 字符
println(x.isNullOrBlank())
```

### NullPointerException

#### Nullable 和 Non-Null

Kotlin 处理空值的方式与其它几种都不一样，它将变量本身分为 `Nullable` 和 `Non-Null` 类型，前者可以指定为空值，后者不能指定为空值。默认声明时没有指定类型的变量为 `Non-Null`，指定类型的为 `Nullable`。

```kotlin
//  Nullable
var x = null

//  Non-Null
var nonNullString: String = "foo"
//  Wrong!
//  nonNullString = null
```

使用符号 `?` 可以将一个指定类型的变量声明为 `Nullable`

```kotlin
var nullableString: String? = "foo"
nullableString = null
```

这种做法的好处是调用方法时通过返回值类型就可以判断返回值是否可以为空，从而避免无谓的空值判断。

#### 安全操作符

同 Groovy 一样，Kotlin 也提供了 `?.` 这样的安全操作符。

```kotlin
val persons = listOf(null, Person("Peter"))
for (p in persons) {
    println(p?.name)
}
```

结合 `Nullable` 可以写出如下代码来防止空指针异常

```kotlin
var y: Int? = null
val nullableResult: Int? = y?.plus(3)
println(nullableResult)
```

当然也可以使用上一章提到的猫王操作符来解决空指针异常的问题。

#### !! 操作符

当我们为一个 `Nullable` 类型的变量赋值后其本身已经非空了，但是将其赋值给其它变量时仍然需要将其它变量声明为 `Nullabel`，这未免有些多此一举。所以 `Kotlin` 提供了 `!!` 操作符，用于将 `Nullabel` 转换为 `Not-null` 类型。

```kotlin
var y: Int? = null
y = 10
val nullableSum: Int? = y?.plus(3)
val nonNullSum: Int = y!!.plus(3)
```

#### Safe Case

当使用 `as` 进行类型转换时，如果无法转换到目标类型的话，系统会抛出类型转换异常。这时可以使用 `as?` 当无法转换时返回 `null` 来解决该问题。

```kotlin
val xInt: Int? = x as? Int
```


## 总结

- Java 1.8 和 Scala 都提供了 Option 操作
- Groovy 和 Kotlin 都提供了猫王操作符
- Kotlin 提供了 `Nullable` 和 `Non-null` 类型来解决空指针异常


---

项目源码见 [JGSK/_09_null](https://github.com/SidneyXu/JGSK)