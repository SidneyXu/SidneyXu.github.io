---
comments: true
date: 2015-12-09T10:43:05+08:00
description: ""
draft: true
keywords:
- scala
slug: "JGSK-Scala-01.apply"
tags:
- scala
title: JGSK - Scala - 01.apply
toc: true
topics:
- JGSK
---

## apply

### apply

`apply` 是 Scala 中的特殊方法。当代码中使用了 `f(arg1,arg2...)` 这样形式的语句，如果 `f` 不是函数或方法时，则 Scala 会自动调用 `f.apply(arg1,arg2...)`。且当 `f` 为 class 时调用的是 object 的 `apply()` 方法，`f` 为 object 时调用的是 class 的 `apply()` 方法。

在 class 和 object 各自定义一个 `apply()` 方法

```scala
class Twain(var x: Int, var y: Int) {
  override def toString: String = s"x=$x, y=$y"
  def sum() = x + y
  def apply() = println(s"Result is ${x + y}")
}

object Twain {
  def apply(x: Int, y: Int) = {
    new Twain(x, y)
  }
}
```

调用 `apply()` 方法

```scala
//  调用 object 的 apply() 方法
val twain = Twain(1, 2)
println(twain.sum()) //  3

//  调用 class 的 apply() 方法
twain() //  Result is 3
```

object 的 `apply()` 方法常用在为对应 `class` 生成实例，即实现工厂方法的功能。

class 的 `apply()` 方法虽然像上面的例子使用时会让人产生迷惑，但是却很适合用在映射上，实际上  Scala 内部也是这样使用的

```scala
val scores = new mutable.HashMap[String, Int]()
//  根据键从映射中获得值
val bob = scores("Bob") //  实际调用的是 scores.apply("Bob")
```


### update

`update` 也是 Scala 中的特殊方法。当代码中使用了 `f(arg1,arg2...) = value` 这样形式的语句，如果 `f` 不是函数或方法时，则 Scala 会自动调用 `f.update(arg1,arg2...value)`。且当 `f` 为 class 时调用的是 object 的 `update()` 方法，`f` 为 object 时调用的是 class 的 `update()` 方法。

在 class 和 object 各自定义一个 `update()` 方法

```scala
class Twain(var x: Int, var y: Int) {
  override def toString: String = s"x=$x, y=$y"
  def update(first: Int, second: Int) = {
    x = first
    y = second
  }
}
object Twain {
  def apply(x: Int, y: Int) = {
    new Twain(x, y)
  }
  def update(x: Int, y: Int, z: Int) = {
    println(x + y + z)
  }
}
```

调用 `update()` 方法

```scala
//  调用 object 的 update() 方法
Twain(1, 2) = 3 //  6

//  调用 class 的 update() 方法
twain(30) = 10
println(twain) //  x=30, y=10
```

object 的 `update()` 方法用得不多，但是 class 的 `update()` 方法和 `apply()` 方法一样适合用在映射上。

```scala
val scores = new mutable.HashMap[String, Int]()
//  向映射中存放值
scores("Bob") = 100 //  实际调用的是 scores.update("Bob,100)
```


### unapply

`unapply()` 又称为提取器，用于对对象进行拆箱，与 `apply()` 正好相反。`unapply()` 定义在 `object` 中且返回值为 `Option` 类型。

在 object 中自定义一个 `unapply()` 方法，该方法接收的是对象本身，返回的是 `Option` 的子类 `None` 和 `Some`。

```scala
object Twain {
  def apply(x: Int, y: Int) = {
    new Twain(x, y)
  }
  def unapply(input: Twain) = {
    if (input.x < 0)
      None
    else
      Some(input.x * 10, input.y * 10)
  }
}
```

调用 `unapply()` 方法

```scala
val Twain(a, b) = Twain(1, 2)
println(s"a=$a, b=$b") //  a=10, b=20
```

以上例子中等式右边的 `Twain` 对象被传入 `unapply()` 方法中进行拆箱，获得的值被赋值给变量 `a` 和 `b`。

熟悉语法的人可以一眼看出 `unapply()` 主要就是用在模式匹配中，如以下例子：

```scala
object Email {
  def unapply(str: String) = new Regex( """(.*)@(.*)""")
    .unapplySeq(str).get match {
    case user :: domain :: Nil => Some(user, domain)
    case _ => None
  }
}
"user@domain.com" match {
  //  实际调用的是 unapply() 方法
  case Email(user, domain) => println(user + "@" + domain)
}
```


## 总结

-  object 的 `apply()` 方法主要用于构造对应的 class 实例，class 的 `apply()` 主要用于从映射中提取值
-  class 的 `update()` 方法主要用于更新对象
-  object 的 `unapply()` 方法主要用于模式匹配

---

项目源码见 [JGSK/s01_apply](https://github.com/SidneyXu/JGSK)

