---
comments: true
date: 2015-09-29T09:55:44+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-24-enum"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 24.枚举"
toc: true
topics:
- JGSK
---



## Java 篇

枚举是种特殊的类，主要用于构建一组特定的值。在 Java 中通常用于限定某个属性的值只能在一定范围内，在某些场合可以被静态常量代替，但是枚举可以有自己的属性也可以有自己的方法，而静态常量无法做到，且静态常量只能限定类型，不能限定值本身。当然在效率上，枚举并不如静态常量。

<!--more-->

创建一个枚举类

一个枚举值的变量名就是该值的名字，枚举值在类中的定义顺序就是该值的索引。

```java
enum WeekDay {
    Monday("Mon"), Tuesday("Tue"), Wednesday("Wed"), Thursday("Thu"),
    Friday("Fri"), Saturday("Sat"), Sunday("Sun");

    private String abbr;

    WeekDay(String abbr) {
        this.abbr = abbr;
    }

    public boolean isWorkingDay() {
        return !(this == Saturday || this == Sunday);
    }

    public String getAbbr() {
        return abbr;
    }
}
```

使用该枚举类

```java
//  直接获得枚举值
WeekDay mondy = WeekDay.Monday;

//  通过名字获得枚举
WeekDay monday = WeekDay.valueOf("Monday");
assert monday == WeekDay.Monday;

//  调用枚举中的方法
assert WeekDay.Tuesday.isWorkingDay();
System.out.println(WeekDay.Thursday.getAbbr());

//  获得一个枚举值的索引
System.out.println(monday.ordinal());

//  通过索引获得枚举值
System.out.println(WeekDay.values()[0]);
```

在 Java 中，枚举还有另一种用法，就是创建单例类。Java 中创建一个单例类通常需要做不少事情，还要考虑到线程的同步，而使用枚举则完全不用考虑这些。一般来说应该尽量用枚举来实现单例。

```java
enum Singleton {
    INSTANCE;
    private int count;
    public int sequence() {
        return ++count;
    }
}
```

使用该单例

```java
Singleton.INSTANCE.sequence()
```

## Groovy 篇

Groovy 中的枚举基本与 Java 一样。

创建一个枚举类

```groovy
enum WeekDay {
    Monday('Mon'), Tuesday('Tue'), Wednesday('Wed'), Thursday('Thu'),
    Friday('Fri'), Saturday('Sat'), Sunday('Sun')

    def abbr

    WeekDay(abbr) {
        this.abbr = abbr
    }

    def isWorkingDay() {
        !(this == Saturday || this == Sunday)
    }
}
```

使用该枚举类

```groovy
//  直接获得枚举值
def monday = WeekDay.Monday

//  通过名字获得枚举
def monday = 'Monday' as WeekDay
assert monday == WeekDay.Monday

//  调用枚举中的方法
assert WeekDay.Tuesday.isWorkingDay()
println(WeekDay.Thursday.abbr)

//  获得一个枚举值的索引
println(monday.ordinal())

//  通过索引获得枚举值
println(WeekDay.values()[0])
```

同 Java 一样，Groovy 也可以通过枚举构建单例。

```groovy
enum Singleton {
    INSTANCE
    def count = 0
    def sequence() {
        ++count
    }
}
```

使用该单例

```groovy
println(Singleton.INSTANCE.sequence())
```

## Scala 篇

严格来说，和其它语言不同，Scala 并没有枚举这种类型。Scala 中的枚举只是一个继承自 `Enumeration` 的 Object。

和其它语言不一样，Scala 并不能任意定义构造方法来构造枚举。Scala 中的枚举值只是 `Enumeration` 下的 `Value` 类的实例，而不是枚举自身的实例，而 `Value` 类的实例主要依靠其构造方法的两个值:`id` 和 `name` 来构建。其中 `id` 是自增长的，`name` 如果不指定时默认使用的就是值的名字。

创建一个枚举类

```scala
object WeekDay extends Enumeration {
  type WeekDay = Value
  val Monday = Value(100, "Mon")
  val Tuesday = Value("Tue")
  val Wednesday = Value
  val Thursday = Value("Thu")
  val Friday = Value("Fri")
  val Saturday = Value("Sat")
  val Sunday = Value("Sun")

  def isWorkingDay(d: WeekDay) {
    !(d == Saturday || d == Sunday)
  }
}
```

使用该枚举类

```scala
//  直接获得枚举值
val monday = WeekDay.Monday

//  通过名字获得枚举
val monday = WeekDay.withName("Mon")
println(monday)

//  调用枚举中的方法
println(WeekDay.isWorkingDay(WeekDay.Tuesday))

//  通过 id 获得枚举值
println(WeekDay(101))

//  获取 id
println(monday.id)
println(WeekDay.Tuesday.id)

//  获取 name
println(WeekDay.Thursday)
println(WeekDay.Wednesday)
```

## Kotlin 篇

Kotlin 中的枚举基本与 Java 一样。

创建一个枚举类

```kotlin
enum class WeekDay(val abbr: String) {
    Monday("Mon"), Tuesday("Tue"), Wednesday("Wed"), Thursday("Thu"),
    Friday("Fri"), Saturday("Sat"), Sunday("Sun");

    fun isWorkingDay(): Boolean {
        return !(this == Saturday || this == Sunday)
    }

}
```

使用该枚举类

```kotliin
//  直接获得枚举值
val monday = WeekDay.Monday

//  通过名字获得枚举
val monday = WeekDay.valueOf("Monday")
println(monday)
println(monday.name)

//  调用枚举中的方法
println(WeekDay.Tuesday.isWorkingDay())

//  获得一个枚举值的索引
println(monday.ordinal

//  通过索引获得枚举值
println(WeekDay.values()[0])
```



## 总结

- 除了 Scala 之外，其它三种语言枚举使用方法基本一致
- Scala 没有真正意义上的枚举类型

---

项目源码见 [JGSK/_24_enum](https://github.com/SidneyXu/JGSK)

