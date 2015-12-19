---
comments: true
date: 2015-10-13T00:29:32+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "2015-JGSK-io"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 28.IO.
toc: true
topics:
- JGSK
---

## 共通篇

Java 中的 IO 操作主要是对各种流进行操作。Java 中流可以分为字节流(InputStream)，字符流(FileReader)，转换流(InputStreamReader)等。这种设计方式一直都被认为是非常优秀的。
由于流的种类繁多，使用起来有固定的套路，并不复杂，所以本章只是简单介绍一下。

<!--more-->

## Java 篇

### 创建文件

Java 中文件和文件夹都属于 File 对象，本质上没有任何区别。但是使用文件夹时必须先手动创建，而创建文件时必须先保证所在的文件夹存在。

```java
String filepath = "files/test.txt";
File file = new File(filepath);
if (!file.exists()) {
    try {
        file.createNewFile();
    } catch (IOException e) {
        e.printStackTrace();
    }
} else {
    file.delete();
}
```

一般而言 Java 中向一个不存在的文件写入数据时文件会被自动创建，所以无需手动调用 `createNewFile()` 方法。但是当文件名没有后缀或者说以 `.` 开头时则必须手动先创建，因为 Java API 这时会误认为需要创建的是文件夹而不是文件。

### 写入操作

在 Java 7 以前IO 操作一般都需要放在 `try..catch..finally` 块中，在最开始定义 Stream类型 的变量，在 `finally` 中关闭该 Stream。

```java
FileOutputStream outputStream = null;
try {
    outputStream = new FileOutputStream(file, true);
    outputStream.write("hello java".getBytes());
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (null != outputStream) {
        try {
            outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

Java 7 中由于 `try..resource` 的出现代码，资源文件会被自动关闭，因此可以进一步进行简写。

```java
try (FileOutputStream fos = new FileOutputStream(file, true)) {
    fos.write("hello java".getBytes());
} catch (IOException e) {
    e.printStackTrace();
}
```

### 读取操作

```java
try (FileReader reader = new FileReader(file);
     BufferedReader bufferedReader = new BufferedReader(reader)) {
    String result;
    StringBuilder stringBuilder = new StringBuilder();
    while ((result = bufferedReader.readLine()) != null) {
        stringBuilder.append(result);
    }
    System.out.println(stringBuilder.toString());
} catch (IOException e) {
    e.printStackTrace();
}
```

## Groovy 篇

### 创建文件

同 Java。

### 写入操作

Groovy 不支持 `try..resource`，但是提供了类似的 `withStream` 等自动关闭资源的闭包。

```groovy
new FileOutputStream(file, true).withStream {
    it.write("hello groovy".getBytes())
}
```

除此之外，Groovy 也提供了一些简便方法

使用 FileWriter 直接向文件中插入数据

```groovy
def file = new File(filepath)
file.append("hello world\n")
```

或者整个替换

```groovy
def content = file.text
file.text = "$content\nend writer"
```

### 读取操作

```groovy
new FileInputStream(file).withStream {
    it.eachLine { line ->
        println(line)
    }
}
```


## Scala 篇

### 创建文件

同 Java。

### 写入操作

Scala 使用 Java 的 API 来执行写入操作，但是并不支持 `try..resource`，也没有提供其它自动关闭资源的方式。只能自己实现或使用第三方库。

```scala
var writer: FileWriter = null
try {
  writer = new FileWriter(file)
  writer.write("hello world")
} catch {
  case e: Exception => e.printStackTrace()
} finally {
  writer.close()
}
```

### 读取操作

与写入操作不同，Scala 对读取操作提供了一些简便方法。需要注意的是这些方法也都不会自动关闭，必须手动 `close()`。

```scala
var source = Source.fromFile(file)
    val lineIterator = source.getLines()
for (l <- lineIterator) {
  println(l)
}
source.close()

source = Source.fromFile(file, "UTF-8")
val contents = source.mkString
println(contents)
source.close()
```


## Kotlin 篇

### 创建文件

同 Java。

### 写入操作

```kotlin
var fos = source.outputStream();
fos.write("hello kotlin".toByteArray())
fos.close()
```

Kotlin 目前也支持了自动关闭资源的功能，所以以上代码可以改为以下形式。

```kotlin
source.outputStream().use {
    it.write("hello kotlin".toByteArray())
}
```

除此之外，Kotlin 也提供了一些简便方法直接向文件中插入数据

```kotlin
var source = File(filepath)
source.appendBytes("hello world".toByteArray())
```

### 读取操作

注意以下这些读取操作都会自动关闭资源

```kotlin
val lines = source.readLines("UTF-8")
for (l in lines) {
    println(l)
}

val contents = source.readText("UTF-8")
println(contents)
```

## 总结

- Java, Groovy 和 Kotlin 支持自动关闭资源，Scala 需要自己编写闭包来实现此功能
- 其它三种语言事实上都是通过 Java API 进行读写操作的


---

项目源码见 [JGSK/_28_io](https://github.com/SidneyXu/JGSK)
