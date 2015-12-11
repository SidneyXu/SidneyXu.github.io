---
comments: true
date: 2015-09-22T00:31:32+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JSGK-15-class-groovy"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 15.类 - Groovy 篇
toc: true
topics:
- JGSK
---

## 定义一个类

Groovy 中也使用关键字 `class` 来定义类

```groovy
class Person {
    //  属性
    def age
    //  行为
    def say(message) {
        println(message)
    }
}
```

<!--more-->

新建一个类的实例同 Java

```groovy
def person = new Person()
```

## Setter 与 Getter

与 Java 不同，默认当你在 Groovy 类中定义了属性的同时 Groovy 提供了对应的 Setter 与 Getter 方法，且当你直接使用属性时其实就是调用这些方法。

例

```groovy
def person = new Person()
//  调用属性修改 age
person.age = 10
assert person.age == 10

//  调用方法修改 age
person.setAge(12)
assert person.age == 12
```

可以看到为 `Person` 类定义了属性 `age` 后，无论直接调用属性还是调用方法都改变了 `age` 的值。

实际上以上的 `Person` 会被翻译成如下代码

```java
public class Person implements GroovyObject {
    private Object age;
 
    public Person() {
        CallSite[] var1 = $getCallSiteArray();
        MetaClass var2 = this.$getStaticMetaClass();
        this.metaClass = var2;
    }

    public Object getAge() {
        return this.age;
    }

    public void setAge(Object var1) {
        this.age = var1;
    }
}
```

当然你也可以像 Java 一样在类中自定义 Setter 和 Getter 方法

例

```groovy
class Person {

    private def privateAge

    //  自定义 privateAge 的 Setter 和 Getter 方法
    def getPrivateAge() {
        return privateAge
    }

    def setPrivateAge(n) {
        if (n > privateAge) {
            privateAge = n
        }
    }
}
```

调用自定义的 Setter 和 Getter

```groovy
person.privateAge = 20
person.privateAge = 14
assert person.age == 20
```

当我们再次修改 `privateAge` 的值为 `14` 时，由于自定义的 Setter 方法不允许年龄被减少，所以这次修改并没成功。这一例子也证明了调用属性其实是调用方法。

>Groovy 中当你定义了一个没有访问控制符的属性时，语言会自动提供 Setter 和 Getter 方法，此时这种属性在 Groovy 中被称作为 `Properties`。而如果属性本身就声明为 public 的话，则不会提供 Setter 和 Getter，这种属性被称作 `Fields`。本系列中都统一称作为属性，因为 `Public Fields` 本身就并不建议使用。

## 构造方法

Groovy 中不用像 Java 那样手动创建一个个构造方法，Groovy 会自动为我们生成。

```groovy
class Man {
    def name
    def age
    private def from = "USA"
    def description = "none"
}
```

在创建时可以通过 `propertyName: propertyValue` 指定使用哪个属性来构造实例

```groovy
def fred = new Man(name: "Fred", age: 21)
def peter = new Man(name: "Peter")
def jack = new Man(age: 21)
def terry = new Man(from: "LA", description: "A man from LA.")
```

以上四个实例都可以正常使用，如果细心的话，你会发现 `from` 被声明为 `private`，虽然无法通过对象进行修改，但是通过构造方法修改它的值却是可能的，很不幸这也是 Groovy 的一个历史遗留 Bug。

## 不可变对象

Groovy 中可以为一个类添加注解 `@Immutable` 来表明该类的所有属性都不可变。一旦一个类声明为 `@Immutable` 则此类的所有属性必须明确指定类型而不能使用 `def` 来定义。

```groovy
@Immutable
class ImmutableSong {
    String name
    String artist
    Date publishDate
}
```


---

项目源码见 [JGSK/_15_class](https://github.com/SidneyXu/JGSK)

