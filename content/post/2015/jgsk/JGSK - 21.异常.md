---
comments: true
date: 2015-09-29T09:53:24+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-21-exception"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 21.异常"
toc: true
topics:
- JGSK
---


## Java 篇

Java 中异常是种特殊对象，可以分为检查异常和非检查异常，所有 RuntimeException 都属于非检查异常。

非检查异常表示程序正常运行时不应该会发生的异常，所以无需对非检查异常进行额外的检查。

<!--more-->

```java
int i = 5/0;
```

检查异常属于程序正常运行时也可能会发生的异常，Java 要求必须对检查异常手动进行try..catch 或者通过方法声明向上抛出。

通过方法声明抛出异常

```java
public void test() throws MyException, FileNotFoundException {
}
```

手动抛出异常

```java
throw new FileNotFoundException("File not found.");
```

一般来说可以在每个方法中都使用方法声明抛出异常然后在最上层使用 try..catch 捕获异常

```java
try {
    // do something
} catch (MyException e) {
} catch (FileNotFoundException e) {
} finally {}
```

其中无论异常是否会发生，finally 的代码块总是会被执行。

在 Java 1.7 后以上代码还可以简写为

```java
try {
    // do something
} catch (MyException | FileNotFoundException e) {
} finally {}
```

Java 这种处理方式有时会让程序变得非常啰嗦，毫无美感，如果遇到回调的话就更是如此

```java
class MyBean {
    interface Callbacks {
        void done();
    }
    public void when(Callbacks callbacks) {
        callbacks.done();
    }
}

try {
    new MyBean().when(() -> {
        try {
        } catch (MyException | FileNotFoundException e) {
            System.out.println("Some errors occur in callback.");
        }
    });
} catch (FileNotFoundException | MyException e) {
    System.out.println("Some errors occur.");
}
```

以上可以看到由于 try..catch 无法捕获回调中的异常，所以回调外和回调内部都要编写 try..catch 语句，如果程序逻辑或者回调更加复杂的话程序简直毫无可读性。

## Groovy 篇

Groovy 中所有异常均为非检查异常，所以无需添加额外的方法声明。

如果你希望的话，也可以像 Java 一样通过方法声明抛出异常，

```groovy
def test() throws MyException {}
```

手动抛出异常

```groovy
throw new FileNotFoundException("File not found.")
```

try..catch..finally 语句

```groovy
try {
    // do something
} catch (MyException | FileNotFoundException e) {}
} finally {}
```

同 Java 一样当遇到回调时 try..catch 语句会变得非常难看，不过在 Groovy 中可以通过闭包来较优雅的解决这个问题

```groovy
class MyBean {

    interface Callbacks {
        def done()
    }

    def when(Callbacks callbacks) {
        tryCatchClosure {
            callbacks.done()
        }
    }

    def tryCatchClosure(Closure closure) {
        try {
            closure();
        } catch (MyException | FileNotFoundException e) {
            println("Some errors occur in closure.")
        }
    }
}

try {
    uncheckedException()
    checkedException()

    new MyBean().when(new MyBean.Callbacks() {

        @Override
        def done() {
            checkedException()
        }
    })
} catch (ArithmeticException | FileNotFoundException | MyException e) {
    println("Some errors occur.")
}
```

## Scala 篇

Scala 中所有异常也都为非检查异常。

尽管没有必要，如果你希望的话，也可以通过注解声明抛出异常

```scala
@throws(classOf[MyException])
def test(): Unit = {
}
```

手动抛出异常

```scala
throw new FileNotFoundException("File not found.")
```

try..catch..finally 语句

```scala
try {
  // do something
} catch {
  case e: MyException =>
  case e: FileNotFoundException =>
} finally {}
```

同 Groovy 一样，Scala 中也可以通过闭包来处理回调中的异常

```scala
class MyBean {

  def when(callback: () => Unit): Unit = {
    try {
      callback()
    } catch {
      case _: Throwable => println("Some errors occur in closure.")
    }
  }
}

try {
  checkedException()

  new MyBean().when2(() => {
    checkedException()
  })
} catch {
  case _: Exception => println("Some errors occur.")
} finally {}
```

在 Scala 中 throw 异常也是由返回值的，并且返回值为 Nothing，所以可以写出以下语句

```scala
val n = 4
val half =
  if (n % 2 == 0)
    n / 2
  else
    throw new RuntimeException("n must be even")
println(half) //  2
```

或者在 try..catch 中处理返回值

```scala
val m = try {
  "99".toInt
} catch {
  case e: Exception => -99
}
println(m)
```

但是需要注意的是不要在 finally 中处理返回值，这是一种很糟的处理方式，会引发不可预知的错误，相信以下例子就可以很好证明这一点

```scala
def f(): Int = try {
  return 1
} finally {
  return 2
}

def g(): Int = try {
  1
} finally {
  2
}

def q(): Int = try {
  return 1
} finally {
}

println(f()) //  2
println(g()) //  1
println(q()) //  1
```

## Kotlin 篇

Kotlin 中所有异常也都为非检查异常。

尽管没有必要，如果你希望的话，也可以通过注解声明抛出异常

```kotlin
@throws(MyException::class, FileNotFoundException::class)
fun test() {
}
```

手动抛出异常

```kotlin
throw FileNotFoundException("File not found.")
```

try..catch..finally 语句

```kotlin
try {
    // do something
} catch(e: FileNotFoundException) {
} catch(e: MyException) {
} finally {}
```

同 Scala 一样，Kotlin 中也可以通过闭包来处理回调中的异常

```kotlin
class MyBean {
    fun callback(callback: () -> Unit) {
        try {
            callback()
        } catch(e: FileNotFoundException) {
            println("Some errors occur in closure.")
        } catch(e: MyException) {
            println("Some errors occur in closure.")
        }
    }
}

try {
  checkedException()

  new MyBean().callback {
    checkedException()
  })
} catch(e: Exception) {
    println("Some errors occur.")
} finally {}
```

在 Kotlin 中同 Scala 一样 throw 异常也是有返回值的

```kotlin
val n = 4
val half =
  if (n % 2 == 0)
    n / 2
  else
    throw new RuntimeException("n must be even")
println(half) //  2
```

或者在 try..catch 中处理返回值

```kotlin
val m = try {
    "99".toInt()
} catch(e: Exception) {
    -99
}
println(m)
```

Kotlin 中并不允许直接在 try..catch..finally 中使用 return 语句，但是还是会发生难以预料的错误

```kotlin
fun f2(): Int {
    try {
        return 1
    } finally {
        return 2
    }
}

fun g(): Int = try {
    1
} finally {
    2
}
println(f2()) //  2
println(g()) //  1
```



## 总结

- 除了 Java 外，其它语言都可以使用闭包来处理回调中的异常
- 只有 Java 拥有检查异常
- Scala 的 try..catch 语句与其它语言都不一样
- Scala 和 Kotlin 中 try..catch 都是表达式，所以可以由返回结果


---

项目源码见 [JGSK/_21_exception](https://github.com/SidneyXu/JGSK)


