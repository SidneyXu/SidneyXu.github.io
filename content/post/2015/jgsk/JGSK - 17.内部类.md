---
comments: true
date: 2015-09-22T12:22:16+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: " JGSK-17-innerclass"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 17.内部类
toc: true
topics:
- JGSK
---


## 共同篇

所谓的内部类即定义在类内部的类，而包含这个内部类的类则被称作外部类。通常来说内部类可以访问外部类的私有成员，作为外部类的内部扩展而存在。

## Java 篇

### 静态内部类

静态内部类即以 `static` 关键字声明的内部类。静态内部类不属于外部类的成员，使用上与普通的外部类没有什么区别。

<!--more-->

定义一个静态内部类

```java
class Outter {
    static class StaticInner {
    }
}
```

创建静态内部类的实例

```java
Outter.StaticInner staticInner = new Outter.StaticInner();
```


### 匿名内部类

匿名内部类即没有指明名字的内部类，通常用于监听器和线程的创建。

```java
Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {

    }
});
```

以上 `new Runnable()` 即定义了一个没有名字的实现了 `Runnalbe` 接口的内部类并立即创建该类的实例作为 `Thread` 的构造方法的参数。

### 非静态内部类

非静态内部类属于外部类，可以被看做为外部类的一个成员，和外部类定义的普通方法和成员变量没有什么区别，所以在该内部类中可以访问外部类的所有成员。也正是这个原因，创建内部类的实例时必须先有外部类的实例。

定义一个非静态内部类

如果内部类和外部类拥有同名成员变量，如果直接使用变量名访问，遵循就近原则，方法的只可能是内部类自身的成员。但是可以通过 `外部类.this.变量名` 的方式来明确指明需要调用的是外部类成员而不是内部类的成员。

```java
class Outter {

    private String name;

    public Outter(String name) {
        this.name = name;
    }

    public class Inner {
        private String name;

        public Inner(String name) {
            this.name = name;
        }

        public String desc() {
            return Outter.this.name + "-" + name;
        }
    }

    public void foo(Inner bar) {
        System.out.println(bar.desc());
    }
}
```

使用该内部类

```java
Outter outter1 = new Outter("Outter1");
Outter.Inner inner1 = outter1.new Inner("Inner1");

Outter outter2 = new Outter("Outter2");
Outter.Inner inner2 = outter2.new Inner("Inner2");

System.out.println("outter1 is " + outter1.getClass()); //  _innerclass.Outter
System.out.println("outter2 is " + outter2.getClass()); //  _innerclass.Outter
System.out.println("inner1 is " + inner1.getClass());   //  _innerclass.Outter$Inner
System.out.println("inner2 is " + inner2.getClass());   //  _innerclass.Outter$Inner

outter1.foo(inner1);    //  Outter1-Inner1
outter1.foo(inner2);    //  Outter2-Inner2
```

以上可以看到 `inner1` 和 `inner2` 两个通过不同外部类实例创建的内部类对象并没有什么区别，可见内部类是属于外部类的。

## Groovy 篇

### 静态内部类

使用同 Java

定义一个静态内部类

```groovy
class Outter {
    static class StaticInner {
    }
}
```

创建静态内部类的实例

```groovy
def staticInner = new Outter.StaticInner()
```


### 匿名内部类

匿名内部类使用也同 Java

```groovy
def thread = new Thread(new Runnable() {
    @Override
    void run() {

    }
})
```

### 非静态内部类

非静态内部类使用也同 Java。但是，在 Groovy 中创建内部类的实例时语法与 Java 不同，使用的是 `new 外部类.内部类(外部类的实例 [, 内部类的构造方法的参数])`，且此时指明内部类的构造方法参数时不能使用带名参数。

定义一个非静态内部类

```groovy
class Outter {

    def name

    static class StaticInner {
    }

    class Inner {
        def name
        Inner(def name) {
            this.name = name
        }
        def desc = "${Outter.this.name}-${name}"
    }

    def foo(Inner bar) {
        println(bar.desc)
    }
}
```

使用该内部类

```groovy
def outter1 = new Outter(name: "Outter1")
//  Wrong!! 不能使用带名参数
//        def inner1 = new Outter.Inner(outter1, name: "Inner1")
def inner1 = new Outter.Inner(outter1, "Inner1")

def outter2 = new Outter(name: "Outter2")
def inner2 = new Outter.Inner(outter2, "Inner2")

println("outter1 is ${outter1.getClass()}") //  _innerclass.Outter
println("outter2 is ${outter2.getClass()}") //  _innerclass.Outter
println("inner1 is ${inner1.getClass()}")   //  _innerclass.Outter$Inner
println("inner2 is ${inner2.getClass()}")   //  _innerclass.Outter$Inner

outter1.foo(inner1) //  Outter1-Inner1
outter1.foo(inner2) //  Outter2-Inner2
```

由以上例子可见在 Groovy 中内部类也是属于外部类的。

## Scala 篇

### 匿名内部类

匿名内部类使用也同 Java

```scala
val thread = new Thread(new Runnable {
  def run(): Unit = {

  }
})
```

### 非静态内部类

Scala 中非静态内部类与 Java 中有很大不同。Scala 中非静态内部类是属于外部类的实例，而不是外部类自身。

定义一个非静态内部类

Scala 中内部类也可以通过 `外部类.this.成员名` 来访问外部类的成员，或者也可以通过像如下的 `outter =>` 定义一个 `外部类.this` 的别名来访问。

```scala
class Outter(val name: String) {
  outter =>

  class Inner(val name: String) {
    def desc = s"${Outter.this.name}-$name"

    def desc2 = s"${outter.name}-$name"
  }

  def foo(bar: Inner): Unit = {
    println(bar.desc)
  }
}
```

使用该内部类

```scala
val outter1 = new Outter("Outter1")
val inner1 = new outter1.Inner("Inner1")

val outter2 = new Outter("Outter2")
val inner2 = new outter2.Inner("Inner2")

println(s"outter1 is ${outter1.getClass}") //  _innerclass.Outter
println(s"outter2 is ${outter2.getClass}") //  _innerclass.Outter
println(s"inner1 is ${inner1.getClass}")  //  _innerclass.Outter$Inner
println(s"inner2 is ${inner2.getClass}")  //  _innerclass.Outter$Inner
```

以上打印时可以看到 `inner1` 和 `inner2` 的类型看起来是一样的。但是调用以下方法后会发现报 `type mismatch` 错误。

```scala
outter1.foo(inner1)
//  Wrong!! type mismatch
//  outter1.foo(inner2)  
```

实际上这是由于 Scala 中存在着一种被称作 "路径依赖类型" 的概念，即 "A.this.B" 随着 A 是不同的实例而不同。所以以上定义的 `foo()` 方法只能接受特定路径的 `Inner` 类的实例。

如果希望 `foo()` 方法接收所有 `Outter` 实例路径下的 `Inner` 类型，可以使用类型投影。类型投影使用符号 `#` 来定义。

例

将 `foo()` 方法换成以下形式

```scala
def foo(bar: Outter#Inner): Unit = {
  println(bar.desc2)
}
```

再调用以下方法就不会报错了

```scala
outter1.foo(inner1)
outter1.foo(inner2)
```

## Kotlin 篇

### 匿名内部类

匿名内部类使用也同 Java

```kotlin
val thread = Thread(Runnable {

})
```

### 非静态内部类

Kotlin 中非静态内部类与 Java 相似。

定义一个非静态内部类

Kotlin 中内部类使用语法 `this@外部类.成员名` 来调用外部成员。

```kotlin
class Outter(val name: String) {

    inner class Inner(val name: String) {
        fun desc() = "${this@Outter.name}-${name}"
    }

    fun foo(bar: Inner) {
        println(bar.desc())
    }
}
```

使用该内部类

```kotlin
val outter1 = Outter("Outter1")
val inner1 = outter1.Inner("Inner1")

val outter2 = Outter("Outter2")
val inner2 = outter1.Inner("Inner2")

println("outter1 is ${outter1.javaClass}")  //  _innerclass.Outter
println("outter2 is ${outter2.javaClass}")  //  _innerclass.Outter
println("inner1 is ${inner1.javaClass}")    //  _innerclass.Outter$Inner
println("inner2 is ${inner2.javaClass}")    //  _innerclass.Outter$Inner

outter1.foo(inner1) //  Outter1-Inner1
outter1.foo(inner2) //  Outter1-Inner2
```


## 总结

- Scala 中非静态内部类属于外部类的对象而非外部类本身，而其它三种语言中则属于外部类
- Scala 中可以定义外部类this的别名
- Scala 与 Kotlin 没有静态外部类
- Groovy 中内部类使用方法与 Java 基本一致，只是创建内部类语法的方式不一样
- Kotlin 使用 this 的语法和其它三种语言都不一样

---

项目源码见 [JGSK/_17_innerclass](https://github.com/SidneyXu/JGSK)

