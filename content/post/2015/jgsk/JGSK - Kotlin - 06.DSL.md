---
comments: true
date: 2015-12-30T12:38:09+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "jgsk-kotlin-06-dsl"
tags:
- kotlin
title: JGSK - Kotlin - 06.DSL
toc: true
topics:
- JGSK
---


## DSL

DSL 即 domain-specific languages，领域特定语言。和一般的编程语言不同，领域特定语言只能用于特定的领域中并且表现形式有限。领域特定语言最大的功能就是可以让语言本身更容易阅读，方便开发者和领域专家进行交流。

<!--more-->

### 实现 DSL

Java 中 DSL 的最简单实现方式就是构造器模式，而在 Kotlin 过去的版本中可以省略 `.`，所以可以写成更易读的代码，但是现在的版本已经不支持了。

构造器模式

```java
Machine machine = new Machine.Builder()
    .setCore(8)
    .setArch("64 bits")
    .setOs("Linux")
    .build();
```

DSL 方式

定义必要的类和方法

```kotlin
data class Cpu(val core: Int, val arch: String)

class Machine {
    var cpu: Cpu? = null
    var os: String? = null

    fun having(cores: Int, arch: String): Machine {
        cpu = Cpu(cores, arch)
        return this
    }

    fun os(os: String): Machine {
        this.os = os
        return this
    }

    override fun toString(): String {
        return "Machine{cpu=$cpu, os='$os'"
    }

}  
```

构建对象

```kotlin
val m1 = Machine().having(8, "64 bits").os("linux")
val m2 = Machine().having(4, "32 bits").os("Windows")
```

可以看到使用 DSL 后代码更加易读。

### 使用闭包构建 DSL

Kotlin 像 Groovy 一样也能通过闭包构建 DSL，语法看起来很像 Groovy。

例

定义必要的类和方法

```kotlin
class EmailSpec {
    fun from(from: String) = println("From: $from")
    fun to(to: String) = println("To: $to")
    fun subject(subject: String) = println("Subject: $subject")
    fun body(init: BodySpec.() -> Unit): BodySpec {
        val body = BodySpec()
        body.init()
        return body
    }
}

class BodySpec {
    fun p(p: String) = println("P: $p")
}

fun email(init: EmailSpec.() -> Unit): EmailSpec {
    val email = EmailSpec()
    email.init()
    return email
}
```

调用 DSL 语句

```kotlin
email {
    from ("dsl-guru@mycompany.com")
    to ("john.doe@waitaminute.com")
    subject ("The pope has resigned!")
    body {
        p ("Really, the pope has resigned!")
    }
}
```

