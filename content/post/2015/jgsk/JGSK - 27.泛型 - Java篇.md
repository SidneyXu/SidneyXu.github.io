---
comments: true
date: 2015-10-04T21:26:22+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-27-generic-java"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 27.泛型 - Java篇
toc: true
topics:
- JGSK
---

## 泛型

### 基本概念

泛型是 Java 1.5才引进的特性。泛型使类型参数化变得可能。在声明类或接口时，可以使用自定义的占位符来表示类型，在运行时由传入的具体类型进行替换。泛型的引入让集合变得更加好用，使很多错误在编译时就能被发现，也省去了一些强制转换的麻烦。

没有泛型的时候使用一个持有特定类型的值的类的时候是非常麻烦的

```java
public class ObjectCapture {
    private Object object;
    public ObjectCapture(Object o) {
        this.object = o;
    }
    public void set(Object object) {
        this.object = object;
    }
    public Object get() {
        return object;
    }
}
```

使用以上类

```java
ObjectCapture integerObjectCapture = new ObjectCapture(10);
assert 10 == (Integer) integerObjectCapture.get();
```

没有泛型的时候在取数据时必须进行强制转换，但是此时根本无法保证 之前使用的 `ObjectCapture` 保存的是 `Integer` 类型的值，如果是其它类型的话，程序就会直接挂掉，而且这种错误只有运行时才能发现。

### 创建泛型

类型参数使用 `<类型参数名>` 作为类型的占位符。

```java
public class Capture<T> {
    private T t;
    public Capture(T t) {
        this.t = t;
    }
    public void set(T t) {
        this.t = t;
    }
    public T get() {
        return t;
    }
}
```

Java 中最常用的占位符为通用的 "T"，表示 Key 的 "K"， 表示 Value 的 "V" 和表示异常的 "E"。

### 使用泛型

```java
Capture<Integer> integerCapture = new Capture<>(10);
assert 10 == integerCapture.get();
Capture<String> stringCapture = new Capture<>("Hi");
assert "Hi".equals(stringCapture.get());
```

以上分别用 `Integer` 和 `String` 作为传入的类型参数，如果向这两个对象传入不符合的类型时编译器就会理解报错，此外取数据时也不用进行强制转换，比起没有泛型时要方便很多。


### 类型擦除

Java 的泛型是在编译器层次实现的，所以运行时有关泛型的信息都会被丢失，这被称作类型擦除。也就是说上节例子中的 `Capture<Integer>` 和 `Capture<String>` 在运行时都是 `Capture` 类型，没有任何区别。

### 协变

如果 Capture<String> 被看做是 Capture<Object> 的子类型，则称这种特性为协变。

在 Java 中，协变是默认支持，所以可以写出以下例子：

```java
Integer[] integers = new Integer[2];
Object[] objects = integers;
```

但是这样会造成以下的问题

```java
Date[] dates = new Date[2];
Object[] objects2 = dates;
objects2[0] = "str";
```

这种代码在编写时完全没有问题，但是运行时会抛出异常。所以引进泛型时就不支持协变，所以以下代码在编译时就会报错。

```java
List<Date> dateList = new ArrayList<>();
List<Object> objectList = dateList;
```

### 类型通配符

由于泛型不支持协变，所以在使用泛型作为参数传递时会非常麻烦。

```java
private static void foo(List<Object> list) {}
```

以上例子中是无法将 `dateList` 传入 `foo()` 方法中的。解决方法是使用通配符 `?`。

```java
private static void foo(List<?> list) {}
```

以上例子中就能正常传入 `dateList` 了。

>注意：List<?> 和 List 并不是一个概念
>
>List 是原生类型，表示不对 List 的类型进行限制，可以进行各种操作，错误使用在运行时才能发现。
>
>List<?> 表示 List 中存放的是某种类型的数据，只是类型本身并不确定。所以无法建立 List<?> 的实例，也无法向 List 中追加任何数据。

### 类型参数边界

上节说过无法向 List<?> 中追加任何数据，这一做法会让程序变得非常麻烦，解决方法就是使用类型参数边界。类型参数边界分为上边界和下边界。

上边界用于限定类型参数一定是某个类的子类，使用关键字 `extends` 指定。下边界用于限定类型参数一定是某个类的超类，使用关键字 `super` 指定。

上边界无法确定容器中保存的真实类型，所以无法向其中追加数据，但是可以获得边界类型的数据

```java
private static void foo3(List<? extends Num> list) {
	//        list.add(new Num(4));
    Num num = list.get(0);
}
```

下边界可以追加边界类型的数据，但是获得数据都只能是 Object 类型

```java
private static void foo4(List<? super Num> list) {
    list.add(new Num(4));
    Object object = list.get(0);
}
```


---

项目源码见 [JGSK/_27_generics](https://github.com/SidneyXu/JGSK)






