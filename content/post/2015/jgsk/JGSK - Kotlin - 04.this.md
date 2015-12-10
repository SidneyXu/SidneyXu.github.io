---
comments: true
date: 2015-12-10T11:21:38+08:00
description: ""
draft: true
keywords:
- kotlin
slug: "JGSK-Kotlin-04.this"
tags:
- kotlin
title: JGSK - Kotlin - 04.this
toc: true
topics:
- JGSK
---

## this

- 在 Java 中 `this` 通常用于指代当前对象，而在 Kotlin 中由于拥有闭包，扩展等特性，`this` 的指代变得更加复杂。
- 就像 Java 可以使用 `类名.this` 类指代外部类的对象，Kotlin 中可以使用 ``this@类名` 来达到同样效果。 

一般来说，Kotlin 中的 `this` 有以下原则

- 类的成员中声明的 `this` 指向当前类的对象
- 扩展类的方法中的 `this` 指向调用该方法的接收者
- 没有任何限定的 `this` 总是尽可能指向最小范围

例

```kotlin
class A { // this 等于 this@A
    inner class B { // this 等于 this@B
        //  扩展方法隐式指向调用者
        fun Int.foo() {
            // this 等于 this@foo

            val funLit = { ->
                println("this of funLit refers to $this")   //  10
            }

            val funLit2 = { s: String ->
                println("this of funLit2 refers to $this")  //  10
            }

            println("this refers to ${this}")   //  10
            println("this@foo refers to ${this@foo}")   //  10

            println("this@A refers to ${this@A}")  //  A
            println("this@B refers to ${this@B}")  //  A$B

            funLit()
            funLit2("foo")
        }

        fun info() {
            // 类的成员方法指向当前对象即内部类 B
            println("the member refers to $this")    // A$B
            10.foo()
        }
    }
}

A().B().info()
```



## 总结

- 可以使用 `this@label` 形式指向外部对象
- 类的成员中声明的 `this` 指向当前类的对象
- 扩展类的方法中的 `this` 指向调用该方法的接收者
- 没有任何限定的 `this` 总是尽可能指向最小范围

---

项目源码见 [JGSK/k04_this](https://github.com/SidneyXu/JGSK)