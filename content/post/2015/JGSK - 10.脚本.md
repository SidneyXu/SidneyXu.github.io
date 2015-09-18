---
comments: true
date: 2015-09-17T11:06:44+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-10-script"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 10.脚本"
toc: true
topics:
- JGSK
---

## 共通篇

脚本，是使用一种特定的描述性语言，依据一定的格式编写的可执行文件。通常可以用于执行一些简单的代码。
<!--more-->

## Java 篇

不支持

## Groovy 篇

### 创建第一个脚本

新建文件 "scriptTest.groovy"，内容如下

```groovy
println("Hello World")
```

在 Console 中执行

```
groovy scriptTest.groovy
```

成功的话可以看到命令行输出 "Hello World" 字符串，可以看到创建脚本时无需写类也无需写 `main()` 方法。

### 在其它类或脚本中执行其它的脚本

Groovy 脚本在编译后实际也会转变为对应的 Java 类，所以上一节的例子在编译后会变成以下的代码

```java
public class scriptTest extends Script {
    public scriptTest() {
        CallSite[] var1 = $getCallSiteArray();
    }

    public scriptTest(Binding context) {
        CallSite[] var2 = $getCallSiteArray();
        super(context);
    }

    public static void main(String... args) {
        CallSite[] var1 = $getCallSiteArray();
        var1[0].call(InvokerHelper.class, scriptTest.class, args);
    }

    public Object run() {
        CallSite[] var1 = $getCallSiteArray();
        return var1[1].callCurrent(this, "Hello World");
    }
}
```

可以看到 Groovy 脚本编译后实际是生成了一个继承自 Script 的类。该类有两个构造方法（空构造方法和 Binding 为参数的构造方法），一个 main() 方法，一个 run() 方法，实际编写的代码其实是被编译到 run() 方法中。所以在其它程序中执行 Groovy 脚本就很容易了，只要 new 一个这个脚本类的实例并执行 run() 方法就可以了。

```groovy
def scope = new scopeScript()
scope.run()
```

这里要注意的是 Groovy 脚本名可以包含 "-" 这样的特殊字符，但是这种字符并不符合 Java 的命名规则，所以脚本名包含特殊字符时无法通过以上方式被执行。

### 本地域与绑定域

Groovy 脚本中定义的变量分为绑定域 (Binding Scope) 与本地域 (Local Scope)。本地域为脚本中使用 `def` 定义的动态类型变量或用特定类型定义的静态类型变量。绑定域为没有任何前缀的变量。

本地域只能被脚本当中的语言调用，在编译后会被声明为 run() 方法中的临时变量，所以也无法被脚本中定义的其它方法调用。

绑定域可以通过脚本外的程序进行赋值，在编译后会被变为脚本类的成员变量，所以可以被脚本中定义的其它方法调用。

例

编写 `scopeScript.groovy` 脚本文件

```groovy
package com.bookislife.langlabs.groovy._07_script

//local scope
String hello = "hello"
def world = "world"

//binding scope
helloworld = "hello world"

void otherMethod() {
    //  Wrong!! 无法访问本地域 hello 和 world
//    println hello
//    println world

    println(helloworld)
}

println "${hello} ${world}, ${helloworld}"

//  脚本中定义的方法只能访问绑定域
otherMethod()
```

编写 `Scripts.groovy` 类文件，以此来调用 `scopeScript.groovy` 脚本文件

```groovy
package com.bookislife.jgsk.groovy._10_script

class Scripts {

    public static void main(String[] args) {
        //  Run Scripts
        def scope = new scopeScript()

        //  Binding Values
        scope.binding.hello = "goodbye"       //    hello is not a binding scope
        scope.binding.helloworld = "hello groovy"   //  helloworld is a binding scope
        scope.otherMethod()
    }
}
```

通过以上可以看到使用 "binding" 属性可以为脚本中的绑定域进行赋值，但是无法修改本地域。

## Scala 篇

### 编写脚本

Scala 脚本不会被编译成类文件，所以没有 Groovy 脚本那么强大的功能。

例

编写一个脚本文件，`scriptTest.scala`

```scala
println(s"Hello World, ${if (args.nonEmpty) args(0) else ""}")
```

在命令行执行以下代码

```
scala scriptTest.scala Peter
```

成功的话会输出 "Hello World, Peter"。

可以看到 Scala 脚本中使用数组 `args` 来存储输入的参数。

### 在 IDEA 中运行脚本

和 Groovy 不一样，如果试图在 IDEA 中运行 Scala 脚本的话会无法执行或者运行时报找不到符号。

如果确实需要在 IDEA 中运行 Scala 脚本的话需要先做以下设置

1. 打开 "Run" -> "Edit Configurations"  
  ![][01]
2. 点击左上角的 "+" 号，选择 "Scala Script"  
  ![][02]
3. 输入任意 Name，在 "Script file" 一项中选择要运行的脚本文件
4. 在 "Before launch" 一项中删除 "Make" 操作
5. 上述完成后就可以运行脚本了
![][03]

## Kotlin 篇

不支持


## 总结

- Groovy 和 Scala 支持脚本操作，但是 Groovy 的功能更加强大
- Idea 可以直接运行 Groovy 脚本，但不支持直接运行 Scala 脚本


---

项目源码见 [JGSK/_10_script](https://github.com/SidneyXu/JGSK)

[01]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/10/select_conf.png
[02]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/10/select_script.png
[03]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/10/del_make.png
