---
comments: true
date: 2015-09-22T00:31:17+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JSGK-15-class-java"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 15.类 - Java 篇
toc: true
topics:
- JGSK
---

## 总述

类在面向对象编程中是一个最基本的概念。类是对象的模板，用于产生具有相同结构的对象。一个类通常由属性和行为构成。

## 定义一个类

Java 中使用关键字 `class` 来定义类
<!--more-->

```java
public class Person {
    //  属性
    private int age;
    //  行为
    public void say(String message) {
        System.out.println(message);
    }
}
```

新建一个类的实例则使用关键字 `new`

```java
Person person = new Person();
```

## Setter 与 Getter

一般来说由于类的封装性以及 Java Bean 的规约，在 Java 中通常将属性声明为私有属性，然后提供公有的 Setter 和 Getter 方法来修改和访问属性。

最常见的 Setter

```java
public void setAge(final int age) {
    this.age = age;
}
```

最常见的 Getter

```java
public int getAge() {
    return age;
}
```

## 构造方法

构造方法在类中用于构造对象，提供一些在对象创建时就应该被赋予的属性。在 Java 中，默认每个类都有一个无参的构造方法，但是如果你已经在类中定义了构造方法，那么默认的无参构造方法就不好被创建。

```java
public class Man {

    private String name;
    private int age;
    private final String from = "USA";
    private String description = "none";

    public Man() {
    }

    public Man(String name, int age, String description) {
        this.age = age;
        this.description = description;
        this.name = name;
    }

    public Man(String name, int age) {
        this(name, age, null);
    }

    public Man(int age) {
        this(null, age);
    }

    public Man(String name) {
        this(name, 0);
    }
}
```

在 Java 中，一般都会提供了一个可以为所有公开属性赋值的构造方法，然后重载只有定义属性的构造方法，并且在这些构造方法中调用拥有所有公开属性的构造方法。

基于构造方法构建实例

```java
Man fred = new Man("Fred", 21);
Man peter = new Man("Peter");
Man jack = new Man(21);
```

可以看到，如果一个类属性很多并且在构造时有多种途径时，那么重载构造方法会是一种非常繁琐无聊的工作。

## 不可变对象

不可变对象在并发编程中占有非常重要的地位。但是Java 中并没有提供用于产生不可变对象的类，你必须手动定义才行。最简单的定义方法就是将类的所有属性声明为 `private final` 并且在构造方法中构造所有属性，且不提供任何 Setter 方法，Getter 方法只返回基本类型数据或者引用类型数据的不可变形态或副本。

```java
public class ImmutableSong {
    private final String name;
    private final String artist;
    private final Date publishDate;

    public ImmutableSong(final String name,
                         final String artist,
                         final Date publishDate) {
        this.artist = artist;
        this.name = name;
        this.publishDate = publishDate;
    }

    public String getArtist() {
        return artist;
    }

    public String getName() {
        return name;
    }

    public Date getPublishDate() {
        return new Date(publishDate.getTime());
    }
}
```


---

项目源码见 [JGSK/_15_class](https://github.com/SidneyXu/JGSK)
