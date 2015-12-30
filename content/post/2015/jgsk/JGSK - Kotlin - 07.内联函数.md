---
comments: true
date: 2015-12-30T13:13:35+08:00
description: ""
draft: false
keywords:
- kotlin
slug: "jgsk-kotlin-07-inline-function"
tags:
- kotlin
title: "JGSK - Kotlin - 07.内联函数"
toc: true
topics:
- JGSK
---

## 内联函数

### 概念

普通函数在执行时程序会跳转到函数定义处执行完毕后再返回调用处。而内联函数指的是函数定义被整个复制到了调用处，所以与普通函数相比，内联函数无需进行程序的跳转，有助于提高程序的运行速度。但是大量的内联函数会增大应用的体积，从而减慢应用的加载速度。

<!--more-->

特别注意，内联函数不是内嵌函数，内嵌函数是函数中定义函数，两者只差一个字，但是意义完全不同。

Java 平台一直都是支持内联函数的，但是开发者无法自行指定任何函数为内联函数。内联这一操作本身是由 JVM 在运行时自动决定的。而 Kotlin 提供了在编译器进行内联的功能。

### 定义内联函数

定义内联函数只需要在普通函数前面加上 `inline` 关键字即可。需要特别注意的是内联操作不仅对函数体有效，也对函数参数有效。

```kotlin
//  内联函数
inline fun <T> inlineLock(lock: Lock, body: () -> T): T {
    lock.lock()
    try {
        return body()
    } finally {
        lock.unlock()
    }
}

//  普通函数
fun echo() = println("foo")
```

调用该内联函数

```kotlin
val lock = ReentrantLock()
inlineLock(lock, { echo() })
```

以上代码会被编译成以下代码，即函数体和作为参数的 Lambda 表达式都被替换成了实际代码。

```kotlin
val lock = ReentrantLock()
lock.lock()
try {
    println("foo")
} finally {
    lock.unlock()
}
```

### noinline

有时我们希望定义内联函数时只要对函数体有效，不要对参数有效，可以在参数前加上 `noinline` 关键字取消参数的内联操作。

```kotlin
inline fun <T> inlineLock(lock: Lock, body: () -> T,
                          noinline notInlined: () -> T): T {
    lock.lock()
    try {
        return body()
    } finally {
        lock.unlock()
    }
}
```

### Non-local Returns

我们知道在函数中定义的函数或闭包如果调用 `return` 语句只能退出其当前所在的闭合语句块，即本地返回，而无法退出外层函数。而内联函数由于是被直接复制到了调用处，所以其支持非本地返回(Non-local returns)，即调用 `return` 时会退出外层函数。

例

定义一个内联函数

```kotlin
inline fun contains(f: () -> Boolean): Boolean {
    return f()
}
```

定义一个调用内联函数的函数

```kotlin
fun localReturn(list: List<Int>): Boolean {
    contains {
        for (i in list) {
            if (i == 0) return true
        }
        return false
    }
    return false
}
```

以上 `contains` 中的 `return` 语句会直接返回到 `localReturn()` 的调用处，而不是退出 `contains` 语句块。

这种特性在很多时候很有用，但是有时我们定义的内联函数并不会立即在当前环境执行，而是放在诸如线程中执行，这时调用该函数的时候就不能依靠它的返回值来控制流程，为了标志这种情况可以在参数上加上关键字 `crossinline`，这样调用处会在编译时就报错避免错误调用。

例

```kotlin
inline fun crosslineFun(crossinline f: () -> Unit): Unit {
    Thread {
        f()
    }.start()
}
fun crosslineReturn(list: List<Int>): Unit {
    crosslineFun {
    }
}
```


### 类型参数

有时候我们希望将类型作为参数进行传递，在 Java 中主要依赖反射的机制，将 `Class` 对象作为进行传递。

例

```kotlin
fun <T> TreeNode.findParentOfType(clazz: Class<T>): T? {
    var p = parent
    while (p != null && !clazz.isInstance(p)) {
        p = p.parent
    }
    @Suppress("UNCHECKED_CAST")
    return p as T
}
```

调用以上函数

```kotlin
val root = DefaultMutableTreeNode("root")
val node1 = DefaultMutableTreeNode("node1")
val node1_1 = DefaultMutableTreeNode("node1_1")
val node2 = DefaultMutableTreeNode("node2")
node1.add(node1_1)
root.add(node1)
root.add(node2)

var parent = node1_1.findParentOfType(DefaultMutableTreeNode::class.java)
println(parent) //  node1
```

可以看到由于依赖于反射，所以在函数体内不但要处理未检查类型转换的警告也很难处理具体类型的信息。

而内联函数由于是复制到调用处，所以实际在运行时无需依赖反射，可以直接得到真实类型。要开启此功能，只需在泛型参数前加上 `reified` 关键字。

```kotlin
inline fun <reified T> TreeNode.inlineFindParentOfType(): T? {
    var p = parent
    while (p != null && p !is T) {
        p = p.parent
    }
    return p as T
}
```

调用该函数

```kotlin
parent = node1_1.inlineFindParentOfType()
println(parent) //  node1
```