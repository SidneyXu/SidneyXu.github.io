---
comments: true
date: 2015-12-15T22:07:36+08:00
description: ""
draft: false
keywords:
- groovy
slug: "jgsk-groovy-01-high-order-function"
tags:
- groovy
title: "JGSK - Groovy - 01.高阶函数"
toc: true
topics:
- JGSK
---

## 高阶函数


### 函数字面量

所谓的函数字面量指的将函数本身赋值给一个变量。在 Groovy 中，函数字面量是通过闭包来实现的。通过函数字面量，函数本身可以像普通变量一样进行各种操作。

定义一个函数字面量

``` groovy
def sum = { x, y -> x + y }
```



### 高阶函数

高级函数指的是将函数本身作为参数传递给另一个函数或者一个函数返回值是另一个函数。正是由于函数字面值的存在，高阶函数才能得以实现。

#### 函数作为参数

由于 Groovy 中函数字面值通过闭包来实现，所以先定义一个参数为闭包的函数

``` groovy
static def add10(Closure closure) {
  closure(10)
}
```

将满足条件（即能够接收整形 10）的闭包（函数）作为参数传入

``` groovy
add10({ i -> i + 2 })	//	12
```

以上代码可以简写成以下形式，省略掉参数外的括号

``` groovy
add10 { it + 2 }
```

通过此种形式可以传入不同的函数来完成不同的操作而不用像  Java 一样必须先定义好接口。

#### 函数作为返回值

``` groovy
def scale(factor) {
  return { x ->
    x * factor
  }
}
```

### 柯里化

柯里化 (Currying) 指的是将一个接收多个参数的函数分解成多个接收单个参数的函数的一种技术。很可惜，由于 Groovy 本身只是对 Java 做了一层分装，并不是真正支持函数式编程的语言，所以不支持柯里化技术。



### 函数的部分应用

函数的部分应用 (Function Partial Application) 指的是向一个接收多个参数的函数传入部分参数从而获得一个接收剩余参数的新函数的技术。

定义一个函数（闭包）

``` groovy
def show = { String prefix, String msg, String postfix ->
  prefix + msg + postfix
}
```

从左开始部分应用

``` groovy
def applyPrefix = show.curry("(")
println(applyPrefix("foo", ")"))    //  (foo)
```

以上从左开始应用了 `show()` 函数的 `prefix` 参数并返回了以 `msg` 和 `postfix` 为参数的新函数 `applyPrefix`。需要注意的是从左开始部分应用的方法名为 `curry()` ，这里非常容易让人引起歧义，尽管看起来像，但这绝对不是柯里化。不清楚具体是什么原因，Groovy 的这个方法的命名起得非常糟糕。

与从左开始部分应用一样，还有从右开始部分应用的版本

``` groovy
def applyPostfix = show.rcurry(")")
println(applyPostfix("(", "bar"))   //  (bar)
```

还有一个从指定的索引值开始进行部分应用

``` groovy
def applyIndex = show.ncurry(1, "foobar", ")")
println(applyIndex("("))    //  (foobar)
```

