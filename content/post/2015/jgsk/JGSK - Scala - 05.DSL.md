---
comments: true
date: 2015-12-19T12:14:24+08:00
description: ""
draft: false
keywords:
- scala
slug: "jgsk-scala-05-dsl"
tags:
- scala
title: JGSK - Scala - 05.DSL
toc: true
topics:
- JGSK
---

## DSL

DSL 即 domain-specific languages，领域特定语言。和一般的编程语言不同，领域特定语言只能用于特定的领域中并且表现形式有限。领域特定语言最大的功能就是可以让语言本身更容易阅读，方便开发者和领域专家进行交流。

<!--more-->

### 实现 DSL

Java 中 DSL 的最简单实现方式就是构造器模式，而在 Scala 中由于方法调用时可以省略 `.`以及隐式转换的存在，所以可以写成更易读的代码。

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

```scala
case class Cpu(core: Int, arch: String)

class CpuInt(core: Int) {
  def cores(arch: String): Cpu = {
    Cpu(core, arch)
  }
}

class Machine {
  var cpu: Cpu = null
  var os: String = null

  def having(cpu: Cpu): Machine = {
    this.cpu = cpu
    this
  }

  def os(os: String): Machine = {
    this.os = os
    this
  }

  override def toString = s"Machine($cpu, $os)"
}

```

构建对象

```scala
implicit def int2CpuInt(i: Int): CpuInt = new CpuInt(i)

val m1 = machine having (8 cores "64bit") os "Linux"
val m2 = machine having (4 cores "32bit") os "Windows"
```

可以看到使用 DSL 后代码更加易读。但是相比较而言还是 Groovy 更适合编写 DSL。