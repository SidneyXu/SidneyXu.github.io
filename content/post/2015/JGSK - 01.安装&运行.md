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

目录

[TOC]

# Begin

## Overview

本系列主要是对Java，Groovy，Scala和Kotlin这四种JVM上的语言做一些粗浅的对比。主要使用的开发环境为 Windows 和 Mac，IDE 为 IDEA 和 Eclipse，构建工具为 Maven 和 Gradle。

## 开发环境

本系列的主要开发环境如下

- OS
  
  	- Linux, Mac, Windows(安装 Cygwin)
  
- 语言
  
  	- Java 1.8.0_25
  	- Groovy 2.4.4
  	- Scala 2.11.5
  	- Kotlin 0.12.613
  
- IDE
  
  	- IDEA 或 Eclipse
  
- 构建工具
  
  	- Maven 或 Gradle

## 安装开发语言

- Java

	软件下载地址 [www.java.com/en/download/](https://www.java.com/en/download/)

	注意，如果以上将 “en" 替换成 “zh_CN" 或者通过百度自行搜索会下载绑定百度垃圾组件的Java安装程序，小心不要中招。

	- Windows 具体安装步骤参照这里：[this](https://www.java.com/zh_CN/download/help/windows_offline_download.xml#download)

	- Mac 具体安装步骤参照这里：[this](https://www.java.com/zh_CN/download/help/mac_install.xml)

<!--more-->

- Groovy

	Windows 安装包下载 [this](http://www.groovy-lang.org/download.html)

	Mac 直接使用如下命令安装

	```bash
	http://www.groovy-lang.org/install.html
	```

- Scala
	
	安装包下载 [this](http://www.scala-lang.org/download/)

	环境变量设置 [this](http://www.scala-lang.org/download/install.html)

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


## 运行

### Java

### 简介

Java 是一门跨平台的面向对象的静态语言。初期由 Sun 开发，后被 Oracle 收购。作为 Web 开发的王者，这几年 Java 常被人认为已经廉颇老矣，但是随着 Java 8 的发布，Java 本身又开始焕发活力。

### 第一个 Java 程序

在目录 "com/bookislife/langlabs/java/_01_hello" 下建立文件 `Hello.java`，输入以下内容

``` java
package com.bookislife.langlabs.java._01_hello;

public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

在命令行下编译程序

``` 
javac com/bookislife/langlabs/java/_01_hello/Hello.java
```

接着运行代码

``` 
java com.bookislife.langlabs.java._01_hello.Hello
```

成功的话可以看到命令行输出 "Hello, Java!" 字符串。

## Groovy

### 简介

Groovy 是一门动态语言，本质上其实就是 Java。Groovy 本身开发效率比 Java 要高出许多。

### 第一个 Groovy 程序

在目录 "com/bookislife/langlabs/groovy/_01_hello" 下建立文件 `Hello.groovy`，输入以下内容

``` groovy
package com.bookislife.langlabs.groovy._01_hello

class Hello {

    public static void main(String[] args) {
        println("Hello, Groovy!")
    }
}
```

在命令行下编译程序

``` 
groovyc com/bookislife/langlabs/groovy/_01_hello/Hello.groovy
```

接着运行代码

``` 
groovy com/bookislife/langlabs/groovy/_01_hello/Hello.groovy
```

成功的话可以看到命令行输出 "Hello, Groovy!" 字符串。

可以看到 Groovy 编译与运行的参数可以完全相同，省去了 Java 分隔符和后缀名的麻烦。

## Scala

### 简介

Scala 是一门同时具有面向对象特性和函数式特性的静态语言。其语言本身以复杂性著称，被一些人称作为 JVM 上的 C++，和 EJB 一样是款笨重的产品。使用 Scala 写代码时同样的功能可以写成完全不同的代码。此外与 Java 不同，Scala 初期比较激进，废弃的 API 会被直接从代码中移除而不是标记为废弃。

### 第一个 Scala 程序

在目录 "com/bookislife/langlabs/scala/_01_hello" 下建立文件 `Hello.scala`，输入以下内容

``` scala
package com.bookislife.langlabs.scala._01_hello

object Hello {

  def main(args: Array[String]) {
    println("Hello, Scala!")
  }
}
```

在命令行下编译程序

``` 
scalac com/bookislife/langlabs/scala/_01_hello/Hello.scala
```

接着运行代码

``` 
scala com.bookislife.langlabs.scala._01_hello.Hello
```

成功的话可以看到命令行输出 "Hello, Groovy!" 字符串。

可以看到 Scala 编译与执行与 Java 非常相似。



## Kotlin

### 简介

Kotlin 由 JetBrains 开发，也就是开发 IDEA 的公司。Kotlin 可以被看做是 Scala++---， Kotlin 团队认为 Scala 很优秀，但过于复杂。所以 Kotlin 诞生时虽然拥有很多 Scala 的特性，但是语言本身却简单很多。此外 Kotlin 编写的代码可以转换为 Java 代码也可以转换为 Javascript 代码。

### 第一个 Kotlin 程序

在目录 "com/bookislife/langlabs/kotlin/_01_hello" 下建立文件 `hello.kt`，输入以下内容

``` kotlin
package com.bookislife.langlabs.kotlin._01_hello

fun main(args: Array<String>) {
    println("Hello, Kotlin!")
}
```

在命令行下编译程序并打包

``` 
kotlinc-jvm com/bookislife/langlabs/kotlin/_01_hello/hello.kt -include-runtime -d hello.jar
```

接着运行代码

``` 
java -jar hello.jar
```

成功的话可以看到命令行输出 "Hello, Groovy!" 字符串。

可以看到 Kotlin 编译与执行与以上其它语言都不相同，它需要将 Kotlin 源文件打包成可执行的 jar 包后再运行，且打的包包括 Kotlin 的运行环境，之后就可以用普通的 Java 命令进行运行。