---
comments: true
date: 2015-12-28T13:08:20+08:00
description: ""
draft: true
keywords:
- groovy
slug: "jgsk-groovy-03-dsl"
tags:
- groovy
title: JGSK - Groovy - 03.DSL
toc: true
topics:
- JGSK
---

## DSL

DSL 即 domain-specific languages，领域特定语言。和一般的编程语言不同，领域特定语言只能用于特定的领域中并且表现形式有限。领域特定语言最大的功能就是可以让语言本身更容易阅读，方便开发者和领域专家进行交流。

<!--more-->

### 实现 DSL

Java 中 DSL 的最简单实现方式就是构造器模式，而在 Groovy 中由于方法调用时可以省略 `.`，所以可以写成更易读的代码。

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

```groovy
class Cpu {
    int core
    String arch

    @Override
    public String toString() {
        return "Cpu{" +
                "core=" + core +
                ", arch='" + arch + '\'' +
                '}';
    }
}

class Machine {
    Cpu cpu
    String os

    def having(int cores, String arch) {
        cpu = new Cpu(core: cores, arch: arch)
        this
    }

    def os(String os) {
        this.os = os
        this
    }


    @Override
    public String toString() {
        return "Machine{" +
                "cpu=" + cpu +
                ", os='" + os + '\'' +
                '}';
    }
}

def machine() {
    new Machine()
}   
```

构建对象

```groovy
def m1 = machine() having(8, "64bits") os "Linux"
def m2 = machine() having(4, "32bits") os "Windows"
```

可以看到使用 DSL 后代码更加易读。

### 使用闭包构建 DSL

除了通过省略 `.` 构建 DSL，Groovy 也能通过闭包构建 DSL，而且这种 DSL 可读性更高，目前这种 DSL 已经作为配置文件活跃在各种项目中。

例

定义必要的类和方法

```groovy
class EmailSpec {
    void from(String from) { println "From: $from" }

    void to(String... to) { println "To: $to" }

    void subject(String subject) { println "Subject: $subject" }

    void body(Closure closure) {
        def bodySpec = new BodySpec()
        closure.delegate = bodySpec
        closure()
    }
}

class BodySpec {
    void p(String p) {
        println("P: $p")
    }
}

static def email(Closure closure) {
    def email = new EmailSpec()
    closure.delegate = email
    closure()
}
```

调用 DSL 语句

```groovy
email {
    from 'dsl-guru@mycompany.com'
    to 'john.doe@waitaminute.com'
    subject 'The pope has resigned!'
    body {
        p 'Really, the pope has resigned!'
    }
}
```

