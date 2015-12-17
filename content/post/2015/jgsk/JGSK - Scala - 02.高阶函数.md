---
comments: true
date: 2015-12-16T01:10:31+08:00
description: ""
draft: false
keywords:
- scala
slug: "jgsk-scala-02-high-order-function"
tags:
- scala
title: JGSK - Scala - 02.高阶函数
toc: true
topics:
- JGSK
---



## 高阶函数

### 函数字面量

<!--more-->

所谓的函数字面量指的将函数本身赋值给一个变量。通过函数字面量，可以使函数本身表现得和普通变量一样。

定义一个函数字面量

``` scala
val sum = (x: Int, y: Int) => x + y
```

 Scala 在定义函数字面量时支持参数的类型推断，只要确定了返回值的类型，编写参数时就可以省略类型。

``` scala
val sum2: (Int, Int) => Int = (x, y) => x + y
```

以上函数的返回值类型为 `(Int, Int) => Int`，表示函数有两个整型的输入和一个整型的返回值，所以定义参数 `x` 和 `y` 时可以省略参数类型。

Scala 还有一个强大的特性就是可以通过符号 `_` 获得一个已经定义好的方法的函数字面量。

在类中定义一个方法

``` scala
def multiple(x: Int, y: Int): Int = x * y
```

获得该方法的函数字面量

``` scala
val multi = multiple _
//	调用方法
println(multiple(3, 4)) //  12
//	使用函数字面量
println(multi(3, 4)) //  12
```



### 高阶函数

高级函数指的是将函数本身作为参数传递给另一个函数或者一个函数返回值是另一个函数。正是由于函数字面值的存在，高阶函数才能得以实现。

#### 函数作为参数

定义一个参数为函数类型的函数

``` groovy
def add10(f: (Int) => Int) = f(10)
```

以上定义了一个函数 `add10()`，该函数接收一个以整型为输入并返回整型的函数类型参数，在函数体中会调用传入的函数并向其传入参数 10。

调用以上方法

``` groovy
add10({ i => i + 2 })	//	12
```

以上代码可以简写成以下形式，省略掉参数外的括号

``` groovy
add10({ i => i + 2 })	//	12
```

通过使用占位符可以进一步缩减代码

``` scala
add10(_ + 2)
```

通过此种形式可以传入不同的函数来完成不同的操作而不用像  Java 一样必须先定义好接口。

#### 函数作为返回值

``` groovy
def scale(factor: Double) = (x: Double) => x * factor
```

### 柯里化

柯里化 (Currying) 指的是将一个接收多个参数的函数分解成多个接收单个参数的函数的一种技术。

Scala 中可以直接定义柯里化函数。

定义一个普通函数

``` scala
def minus(x: Int, y: Int) = x - y
```

定义一个柯里化函数

``` scala
def minus2(x: Int)(y: Int) = x - y
```

和普通函数 `minus()` 不同，`minus2()` 有两个参数列表，每个接收一个参数，也就是说 `minus2()` 实际就是对 `minus()` 进行柯里化后的结果。

调用以上函数

``` scala
minus(5, 3)	//	2
minus2(5)(3)	//	2
```

可以看到使用普通函数时必须一次性传入所有参数，而对于柯里化函数可以每次只传入一个参数。

对于像 `minus()` 这样已经定义好的函数在 Scala 中也可以通过 `curried()` 函数将其转变为柯里化函数。

``` scala
val minusCurr=(minus _).curried
minusCurr(5)(3)
```



### 函数的部分应用

函数的部分应用 (Function Partial Application) 指的是向一个接收多个参数的函数传入部分参数从而获得一个接收剩余参数的新函数的技术。

定义一个多参数的函数

``` groovy
def show(prefix: String, msg: String, postfix: String) = prefix + msg + postfix
```

部分应用

``` groovy
val applyPrefix = show("(", _: String, _: String)
println(applyPrefix("foo", ")")) //  (foo)

val applyPostfix = show(_: String, _: String, ")")
println(applyPostfix("(", "bar")) //  (bar)
```

以上 `applyPrefix()` 是应用了 `show()` 的第一个参数的新函数，`applyPostfix()` 是应用了 `show()` 的最后一个参数的新函数。

Scala 中使用符号 `_` 表示暂时不应用的参数，需要注意这些参数必须明确指明参数类型，Scala 没有办法在这种场合做类型推断。



### 偏函数

函数指对于所有给定类型的输入，总是存在特定类型的输出。

偏函数指对于某些给定类型的输入，可能没有对应的输出，即偏函数无法处理给定类型范围内的所有值。

Scala 中偏函数使用 trait `PartialFunction` 表示，该函数是个一元函数，以模式匹配的结果作为函数的最终结果

定义一个偏函数

``` java
val isEven: PartialFunction[Int, String] = {
  case x if x != 0 && x % 2 == 0 => x + " is even"
}
```

以上偏函数虽然接收 `Int` 类型作为参数，但是只能处理 `Int` 不为 0 或者参数为奇数的情况。

使用偏函数

``` scala
isEven(20)
```

偏函数可以通过 `isDefinedAt()` 方法来判断其是否能够处理传入的参数

``` scala
println(isEven.isDefinedAt(4))  //  true
println(isEven.isDefinedAt(3))  //  false
```

当某个偏函数无法处理输入的值后可以通过链式操作将其传递给其它偏函数进行处理

``` scala
val isOdd: PartialFunction[Int, String] = {
    case x if x % 2 != 0 => x + " is odd"
}
val other: PartialFunction[Int, String] = {
    case _ => "else"
}
val partial = isEven orElse isOdd orElse other
println(partial(3)) //  3 is odd
println(partial(0)) //  else
```




### 柯里化函数 (Currying Function)与部分应用函数(Partial Applied Function)与偏函数 (Partial Function)

#### 柯里化函数与部分应用函数

这两种概念初次见到时非常容易让人迷惑，感觉好像差不多，但是实际上是完全不同的函数。

柯里化是将多参数函数分解成多个但参数的函数，即将 `function(x, y, z){...}` 分解成 `function(x){ lambda(y){ lambda(z){...} } }`。由于每次都是传入一个参数后获得包含其余参数的函数所以需要使用 `function(x)(y)(z)` 完成完整调用。柯里化函数非常像责任链模式，参数必须依照定义的顺序由前之后传入，每个参数完成部分工作。

部分应用是只传入部分参数从而获得包含其余参数的函数，即将 `function(x, y, z){...}` 变成 `function(x, _, _)`。本质上部分应用函数用于固定多参数函数的某几个参数，从而在调用时不用每次都传入同样的参数。部分应用函数的参数之间没有任何关系，所以可以部分应用任意位置的参数。

最后，直观上看柯里化函数虽然有多个参数列表，但每个只接受一个参数。而部分应用函数只有一个参数列表，但参数个数至少为 2。

#### 部分应用函数与偏函数

这两个名词虽然是不同的东西，但是由于英文名和部分应用函数的另一个名字（偏函数应用）非常相似所以不注意也容易搞混。

部分应用函数是固定多参数函数的部分参数得到一个新函数。

偏函数则表示算式无法处理传入的参数类型的某些特定值。
