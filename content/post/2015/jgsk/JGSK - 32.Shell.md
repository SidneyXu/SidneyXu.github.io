---
comments: true
date: 2015-11-24T07:00:22+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-32-Shell"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 32.Shell
toc: true
topics:
- JGSK
---

## Java

### 执行 Shell 命令

Java 中执行 Shell 命令主要依靠 Runtime 和 Process 两个类。

```java
 try {
    Runtime runtime = Runtime.getRuntime();
    Process process = runtime.exec("ls -al ..");
    //	等待 Shell 执行的结果，0 为正常执行完毕
    int exitValue = process.waitFor();
    if (exitValue != 0) {
        System.out.println("exit with " + exitValue);
        return;
    }
    //	输出 Shell 执行的结果
    try (InputStream in = process.getInputStream();
         InputStreamReader isr = new InputStreamReader(in);
         BufferedReader reader = new BufferedReader(isr)) {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
    }

} catch (Exception e) {
    e.printStackTrace();
}
```

以上例子可以看到在 Java 中执行 Shell 命令还是比较复杂的，简单的 `ls` 也需要不少代码，不过其中大部分都是普通的 IO 流操作。

### Pipe 操作

Pipe 操作在 Shell 命令中非常常见，可惜的是 Java 无法直接通过执行 Shell 命令来实现此功能，而必须使用执行脚本的方式才能正常执行。

```java
try {
    Runtime runtime = Runtime.getRuntime();
    String[] cmd = {"/bin/sh", "-c", "ls -al .. | grep JGSK"};
    Process process = runtime.exec(cmd);

    int exitValue = process.waitFor();
    if (exitValue != 0) {
        System.out.println("exit with " + exitValue);
        return;
    }

    try (InputStream in = process.getInputStream();
         InputStreamReader isr = new InputStreamReader(in);
         BufferedReader reader = new BufferedReader(isr)) {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
    }

} catch (Exception e) {
    e.printStackTrace();
}
```

相比较直接执行命令而言，只是 `runtime.exec()` 这个方法的参数有了变化。

## Groovy

### 执行 Shell 命令

Groovy 中对 Java 的方法做了一些包装，使用起来更加简单。

```groovy
def p = "ls -al ..".execute()
def exitValue = p.waitFor()
if (exitValue != 0) {
    println("exit with $exitValue")
    return
}
println "${p.text}"
```

### Pipe 操作

Groovy 也无法直接在命令中执行 Pipe 操作，但是却提供了 `pipe()` 方法用以连接两个 `Process`。

```groovy
def p1 = "ls -al ..".execute()
def p2 = "grep JGSK".execute()
def p3 = p1.pipeTo(p2)
exitValue = p3.waitFor()
if (exitValue != 0) {
    println("exit with $exitValue")
    return
}
println "${p3.text}"
```


## Scala

### 执行 Shell 命令

Scala 执行 Shell 命令只要使用 `!` 方法就可以了。

```scala
val exitValue = "ls -al .." !
```

以上例子会进行阻塞并返回执行结果，0 表示正常执行完毕。

```scala
val result = "ls -al .." !!
```

以上例子会进行阻塞并返回执行完毕的输出结果，如果没有正常执行完毕则抛出异常。

通过以上两个例子可以看到 Scala 操作 Shell 简简单单一句话就可以搞定。

### Pipe 操作

Scala 进行 Pipe 操作非常接近实际在 Shell 中执行的命令，只是使用 `#|` 替换了 `|`。

```scala
"ls -al .." #| "grep JGSK" !
```

实际上 `#` 可以与各种操作结合用在多种地方，比如说文件的输出。

```scala
"ls -al .." #> new File("shell.txt") !
"ls -al .." #>> new File("shell.txt") !
```


## Kotlin

### 执行 Shell 命令

Kotlin 目前貌似没有自己的 Api，所以只能调用 Java 的方法。

```kotlin
val runtime: Runtime = Runtime.getRuntime()
val process: Process = runtime.exec("ls -al ..")
val exitValue = process.waitFor()
if (exitValue != 0) {
    println("exit with $exitValue")
    return
}

process.inputStream.bufferedReader().lines().forEach {
    println(it)
}
```

## 总结

- 各种语言都可以进行 Shell 操作，但是只有 Scala 和 Groovy 的 api 具有实际生产力。

---

项目源码见 [JGSK/_32_shell](https://github.com/SidneyXu/JGSK)
