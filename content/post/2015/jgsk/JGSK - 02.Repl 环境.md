---
comments: true
date: 2015-09-15T06:59:50+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-02-repl"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 02.REPL 环境
toc: true
topics:
- JGSK
---


REPL 为 "Read Evaluate Print Loop" 的简写，即 "读取-求值-打印-循环"，是一些动态语言的标准特性，主要用于进行探索式编程。也就是说当你不清楚某项计算的结果或者忘记了某个方法的作用时，可以输入代码片段并立即获得结果。
<!--more-->

## Java 篇

Java 是门纯面向对象语言，入口必须是一个拥有 `main` 方法的类，所以其并不支持 REPL。这意味着如果想计算类似 "2 + 3" 这样的结果，必须打开 IDE，建一个任意名字的类，创建 `main` 方法，然后输入 "System.out.println(2+3)" 才能获得这样一个简单的结果，可以说非常繁琐。

尽管 Java 本身不支持 REPL，但是网上还是有 [在线的 REPL 工具](http://www.javarepl.com/console.html) 可以使用，不过始终没有原生支持好用。

## Groovy 篇

Groovy 提供了一个带有 GUI 界面的 REPL 环境，即 Groovy Console。

只要在命令行输入以下命令就可以直接在当前 Path 下打开 Groovy Console

```
groovyConsole
```

输入语句 `2+3` 后按下 "CTRL + ENTER" 就可以在下面的输出框中看到执行结果

![][01]

默认的 Groovy Console 会保留每次执行的结果，看起来可能不方便，如果希望每次执行时都先清空以前执行的结果需要修改设置 "View" -> "Auto Clear Output On Run"

此外，由于 Groovy 可以直接使用 Java 语言，所以 Groovy Console 也可以当做 Java 的 REPL 环境使用。

## Scala 篇

在命令行中输入 `scala` 就可以进入 Scala 的 REPL 环境，可以看到提示符会变为 "scala>"。

![][02]

输入语句 `2+3` 后回车就可以立即看到结果。

如果希望一次输入多条语句的话可以输入 `:paste` 进入黏贴模式，插入完多行代码后按下组合键 `ctrl+d` 就可以退出黏贴模式并执行刚才编写的多行语句。

## Kotlin 篇

在命令行中输入 `kotlinc-jvm` 或者其简写 `kotlinc` 就可以进入 Kotlin 的 REPL 环境，可以看到提示符会变为 ">>>"。

![][03]

在打开的 GUI 界面中输入语句 `2+3` 后回车就可以立即看到结果。

除此之外，JetBrains 也提供了一个 [在线交互式环境](http://try.kotlinlang.org/) 可以在线编写程序并且能够保存结果。

[01]:	http://7xlqqp.com1.z0.glb.clouddn.com/2015/09/jgsk/01.png
[02]:	http://7xlqqp.com1.z0.glb.clouddn.com/2015/09/jgsk/02.png
[03]:	http://7xlqqp.com1.z0.glb.clouddn.com/2015/09/jgsk/03.png
