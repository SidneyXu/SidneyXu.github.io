---
comments: true
date: 2015-12-20T16:08:20+08:00
description: ""
draft: false
keywords:
- groovy
slug: "jgsk-groovy-02-memorized"
tags:
- groovy
title: JGSK - Groovy - 02.Memorized
toc: true
topics:
- JGSK
---

## Memoized

Memoized 即缓存功能。Groovy 可以对方法和闭包的结果进行缓存，从而再下次传入同样的参数时直接返回缓存的结果。对于那些耗时很久的计算过程来说这一点可以节约不少时间。

<!--more-->

### 对闭包结果进行缓存

调用 `memoize()` 方法就可以对闭包结果进行缓存

```groovy
def closure1 = { Integer x ->
    println "Closure argument $x"
    x
}.memoize()
```

调用闭包

```groovy
closure1(3)
closure1(3)
closure1(3)
closure1(4)
closure1(4)
closure1(4)
```

控制台只有两个输出语句，证明闭包结果被成功缓存了起来。

```
Closure argument 3
Closure argument 4
```

### 对方法的执行结果进行缓存

对方法的执行结果进行缓存需要使用注解 `@Memoized`。

为了区分使用缓存和不使用缓存的区别，本次使用斐波那契数列进行测试。斐波那契数列指的是这样一个数列 `0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89`，即从第 3 项开始每项的值都是其前两项之和。

斐波那契数列的 Groovy 实现

```groovy
int fibonacci(int n) {
    println("fibonacci for $n")
    if (n == 0 || n == 1) return n
    return fibonacci(n - 1) + fibonacci(n - 2)
}
```

由于其中使用了递归所以该方法在使用时会被大量调用

```groovy
fibonacci(5)
```

输出结果

```
fibonacci for 5
fibonacci for 4
fibonacci for 3
fibonacci for 2
fibonacci for 1
fibonacci for 0
fibonacci for 1
fibonacci for 2
fibonacci for 1
fibonacci for 0
fibonacci for 3
fibonacci for 2
fibonacci for 1
fibonacci for 0
fibonacci for 1
```

换成使用缓存的实现

```groovy
@Memoized
static def memoFibonacci(int n) {
    println("memoFibonacci for $n")
    if (n == 0 || n == 1) return n
    return memoFibonacci(n - 1) + memoFibonacci(n - 2)
}
```

调用方法

```groovy
memoFibonacci(5)
```

输出结果

```
memoFibonacci for 5
memoFibonacci for 4
memoFibonacci for 3
memoFibonacci for 2
memoFibonacci for 1
memoFibonacci for 0
```

可以看到重复调用次数比起不使用缓存少了很多，并且接着调用 `memoFibonacci(4)` 之类的都会直接返回结果，因为从 5 以下的结果都已经被缓存了。

Groovy 还可以通过以下方法控制缓存的数量

```groovy
@Memoized(maxCacheSize = 3)
```
