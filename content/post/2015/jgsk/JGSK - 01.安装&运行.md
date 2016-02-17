---
comments: true
date: 2015-09-14T07:08:01+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-01-start"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 01.安装与运行
toc: true
topics:
- JGSK
---

# Begin

## Overview

本系列主要是对 Java，Groovy，Scala 和 Kotlin 这四种 JVM 上的语言做一些粗浅的对比。对于那些语言本身提供但是却不提倡使用的功能本系列大都不会提。本系列主要使用的开发环境为 Windows 和 Mac，IDE 为 IDEA 和 Eclipse，构建工具为 Maven 和 Gradle。
<!--more-->

>除了以上四种 JVM 上的语言，JVM 上还有 Clojure 和 Eclipse 推行的 Xtend 两种语言有一定知名度。其中 Clojure 与其它语言差别过大，不太容易被传统的 Java 程序员接受，我也没学过，所以不放在本系列讨论。而 Xtend 与 Groovy 之类的很相似，但是其一些语法个人不太喜欢（比如说用 "[]" 代替 "{}"，用 "|" 代替 ->" ），总觉得容易误导人，所以也没有深究。

## 开发环境

本系列的主要开发环境如下

- OS
  	- Linux, Mac, Windows(安装 Cygwin)
  
- 语言
  	- Java 1.8.0_25
  	- Groovy 2.4.4
  	- Scala 2.11.5
  	- Kotlin 1.0.0-beta-1038
  
- IDE
  	- IDEA 或 Eclipse
  
- 构建工具
  	- Maven 或 Gradle

## 安装开发语言

- Java

	软件下载地址 [this](https://www.java.com/en/download/)

	> 注意，如果将以上地址中的 “en" 替换成 “zh_CN" 或者通过百度自行搜索会下载绑定百度垃圾组件的Java安装程序，小心不要中招。

	- Windows 具体安装步骤参照这里：[this](https://www.java.com/zh_CN/download/help/windows_offline_download.xml#download)
    	- 环境变量配置：
      		- JAVA_HOME=C:\Program Files\Java\jdk1.8.0_25
      		- Path=%PATH%;%JAVA_HOME%\bin

	- Mac 具体安装步骤参照这里：[this](https://www.java.com/zh_CN/download/help/mac_install.xml)

- Groovy

	- Windows 安装包下载 [this](http://www.groovy-lang.org/download.html)
		- 环境变量配置：
			- GROOVY_HOME=D:\Program Files (x86)\Groovy\Groovy-2.4.4
			- Path=%PATH%;%GROOVY_HOME%\bin

	- Mac 直接使用如下命令安装

	```bash
	brew install groovy
	```

- Scala
	
	安装包下载 [this](http://www.scala-lang.org/download/)

	环境变量设置
    - Windows
        - SCALA_HOME=D:\Program Files (x86)\scala
        - Path=%PATH;%JAVA_HOME%\bin
    - Mac
        - SCALA_HOME=/usr/local/share/scala
        - Path=$PATH:$SCALA_HOME/bin

- Kotlin

	IDEA 配置 [Getting Started](http://kotlinlang.org/docs/tutorials/getting-started.html)

	Eclipse 配置[Getting Started](http://kotlinlang.org/docs/tutorials/getting-started-eclipse.html)

	CLI 配置[Getting Started](http://kotlinlang.org/docs/tutorials/command-line.html)


以上全部安装完毕后可以在命令行中依次执行以下语句来验证是否安装成功

```bash
java -version
scala -version
groovy -v
kotlinc-jvm -version
```

## Java 篇

### 简介

Java 是一门运行在 JVM 上的跨平台的面向对象的静态语言。初期由 Sun 开发，后被 Oracle 收购。如今随着 Java 8的面世，Java 在市场上的份额又开始步步攀升了。

### 第一个 Java 程序

新建一工程 `JGSK`，在该工程下新建一个目录 "com/bookislife/langlabs/java/_01_hello" ，在该目录下建立文件 `Hello.java`，并输入以下内容

```java
package com.bookislife.langlabs.java._01_hello;

public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

然后在命令行下编译程序

```bash
javac com/bookislife/langlabs/java/_01_hello/Hello.java
```

接着运行代码

```bash
java com.bookislife.langlabs.java._01_hello.Hello
```

成功的话可以看到命令行输出 "Hello, Java!" 字符串，第一个 Java 程序就这样运行成功了。

## Groovy 篇

### 简介

Groovy 是一门动态语言，本质上其实就是 Java。Groovy 本身提供了各种工具方法来减少代码量，所以使用 Groovy 的开发效率通常比 Java 要高出许多，但由于动态语言的特性，在运行效率上则要慢一些。不过目前的 Groovy 已经支持了静态编译，在这方面的缺点已经减弱了不少。

### 第一个 Groovy 程序

新建目录 "com/bookislife/langlabs/groovy/_01_hello" ，在该目录下建立文件 `Hello.groovy` 并输入以下内容

``` groovy
package com.bookislife.langlabs.groovy._01_hello

class Hello {

    public static void main(String[] args) {
        println("Hello, Groovy!")
    }
}
```

然后在命令行下编译程序

```bash
groovyc com/bookislife/langlabs/groovy/_01_hello/Hello.groovy
```

接着运行代码

```bash
groovy com/bookislife/langlabs/groovy/_01_hello/Hello.groovy
```

成功的话可以看到命令行输出 "Hello, Groovy!" 字符串。

可以看到 Groovy 编译与运行的参数可以完全相同， 而不用像 Java 一样一会儿有后缀名一会儿没有。

## Scala 篇

### 简介

Scala 是一门同时具有面向对象特性和函数式特性的静态语言。其语言本身以复杂性著称，被一些人称作为 JVM 上的 C++。由于这一特点，使用 Scala 写代码时同样的功能不同的人可以写成完全不同的代码，所以写 Scala 程序时代码规范非常重要。此外与 Java 不同，Scala 比较激进，不少废弃的 API 会被直接从代码中移除而不是标记为废弃。

### 第一个 Scala 程序

新建目录 "com/bookislife/langlabs/scala/_01_hello" ，在该目录下建立文件 `Hello.scala`并输入以下内容

```scala
package com.bookislife.langlabs.scala._01_hello

object Hello {

  def main(args: Array[String]) {
    println("Hello, Scala!")
  }
}
```

在命令行下编译程序

```bash
scalac com/bookislife/langlabs/scala/_01_hello/Hello.scala
```

接着运行代码

```bash
scala com.bookislife.langlabs.scala._01_hello.Hello
```

成功的话可以看到命令行输出 "Hello, Groovy!" 字符串。

可以看到 Scala 编译与执行与 Java 非常相似。

## Kotlin 篇

### 简介

Kotlin 由 JetBrains 开发，也就是开发 IDEA 的公司。Kotlin 可以被看做是 Scala++--， Kotlin 团队认为 Scala 虽然强大，但语言本身过于复杂。所以 Kotlin 诞生时虽然拥有很多 Scala 的特性，但是语法却简单很多。此外 Kotlin 编写的代码可以转换为 Java 代码也可以转换为 Javascript 代码。本篇只介绍 JVM 的版本。

### 第一个 Kotlin 程序

创建目录 "com/bookislife/langlabs/kotlin/_01_hello" ，在该目录下建立文件 `hello.kt`并输入以下内容

```kotlin
package com.bookislife.langlabs.kotlin._01_hello

fun main(args: Array<String>) {
    println("Hello, Kotlin!")
}
```

 然后在命令行下编译程序并打包

```bash
kotlinc-jvm com/bookislife/langlabs/kotlin/_01_hello/hello.kt -include-runtime -d hello.jar
```

接着运行代码

```bash
java -jar hello.jar
```

成功的话可以看到命令行输出 "Hello, Kotlin!" 字符串。

可以看到 Kotlin 编译与执行与以上其它语言都不相同，它需要将 Kotlin 源文件打包成可执行的 jar 包后再运行，且所打的包包括了 Kotlin 的运行环境。
