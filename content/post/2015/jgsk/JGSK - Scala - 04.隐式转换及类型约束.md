---
comments: true
date: 2015-12-17T07:28:35+08:00
description: ""
draft: false
keywords:
- scala
slug: "jgsk-scala-04-implict"
tags:
- scala
title: JGSK - Scala - 04.隐式转换及类型约束
toc: true
topics:
- JGSK
---


## 隐式转换与类型约束

### 隐式操作

#### 隐式转换

隐式转换是通过在指定的上下文环境中定义一个类型转换的函数来在必要时完成变量的自动类型转换。和子类到父类的自动类型转换不同，隐式转换是通过函数来实现的，所以原类型和目标类型可以没有任何关系。

<!--more-->

Scala 中定义一个隐式转换函数只要在普通函数前加上关键字 `implicit` 就可以了。

例

定义一个对 `Int` 类型扩展的类 `SuperInt`

```scala
class SuperInt(val i: Int) {
    def triple = i * i * i
}
```

定义一个隐式转换函数，用于将 `Int` 类型的参数转换为 `SuperInt` 类型。

```scala
implicit def int2SuperInt(i: Int): SuperInt = new SuperInt(i)
```

定义一个使用 `SuperInt` 作为参数的函数

```scala
def showTripleResult(i: SuperInt): Unit = {
    println(s"Result is ${i.triple}")
}
```

使用隐式转换

```scala
val i: Int = 3
showTripleResult(i) //  Result is 27
```

以上例子中 `showTripleResult()` 函数需要 `SuperInt` 类型的参数，而传入的参数为 `Int` 类型，此时编译器会在上下文环境中查找是否有能够将 `Int` 类型参数转换为 `SuperInt` 类型的隐式转换函数。

事实上隐式转换是完全自动完成的，以下例子可以完全体现这点

```scala
println(i.triple) //  27
```

由于隐式转换的功能过于强大，所以必须限定隐式转换的上下文，否则的话会使代码非常难以阅读。最常见的作法是直接定义一个 `Object`，将转换函数都放在里面。

```scala
object Context {
    //  Implicit Conversion
    implicit def int2SuperInt(i: Int): SuperInt = new SuperInt(i)
}
```

然后在使用的地方先进行引用

```scala
import Context._
```


#### 隐式参数与隐式值

隐式参数指的是向函数传入部分参数，余下的参数由编译器自动从上下文环境中寻找符合需要的参数类型的隐式值。

Scala 中定义一个隐式参数只要在参数前加上关键字 `implicit` 就可以了，隐式值也是类似。

例

定义一个包含隐式参数的函数

```scala
def show(msg: String)(implicit prefix: String) = println(s"$prefix $msg")
```

定义一个隐式值

```scala
implicit val stringValue = "Hello"
```

调用该函数

```scala
show("World") //  Hello World
show("Bye")("Good") //  Good Bye
```

`show()` 函数需要接收两个参数，当调用 `show("World")` 时由于缺少了 `String` 类型的隐式参数 `prefix`，Scala 会自动从上下文找寻找存在的 `String` 类型的隐式值 `stringValue` 传入 `show()` 函数。

使用隐式参数时有以下两点需要注意

- 由于隐式参数是根据参数类型来寻找隐式值的，所以如果上下文环境中有多个相同类型的隐式值时，Scala 会报 "ambiguous implicit values" 错误。
- 隐式参数最好使用特别的数据类型，而不要使用 `String` 等常用类型，否则可能会引发问题


#### 隐式类

隐式类可以看做是隐式转换的语法糖，Scala 能够在必要时将源类型自动转换为隐式类的类型。

定义一个隐式类

```scala
object Context {
    implicit class Calculator(i: Int) {
        def add(a: Int) = a + i
    }
}
```

使用该隐式类

```scala
println(3.add(5))   //  8
```

Scala 发现 `Int` 变量 `3` 没有 `add()` 方法时，会自动查找是否能够在上下文环境中将该变量转换为包含 `add()` 方法的目标类型，这里就是类 `Calculator`。

隐式类在使用时有以下原则

- 隐式类必须定义在另外一个类中
- 隐式类的构造器有且只能有一个不是 `implicit` 的参数
- 作用域中不能有任何标示符（方法名，变量名等）与隐式类同名


### 类型约束

类型约束用于对参数类型进行限制，看起来有些像泛型章节所提到的类型参数边界，但是类型约束更加严格。

#### <:<

`<:<` 用于限制参数类型必须为某一个类型本身或该类型的子类。

例

定义三个类型

```scala
class Person(val name: String)

class Student(name: String) extends Person(name)

class Animal(val name: String)
```

定义方法

```scala
def makeFriends[A](a: A)(implicit ev: A <:< Person): Unit = {}
```

以上 `makeFriends()` 方法限制其泛型参数 `A` 必须为类型 `Person` 或其子类

调用该方法

```scala
//  正常调用
val peter = new Person("Peter")
makeFriends(peter)

//  错误调用，会报 type mismatch 异常，因为 Animal 不是 Person 的子类
val cat = new Animal("Cat")
makeFriends(cat)
```

通过以上的例子可以看到 `<:<` 的作用似乎与 `<:` 非常类似，但是前者更为严格。

有如下两个方法，第一个方法使用了上边界，第二个方法使用了类型约束

```scala
def foo[A, B <: A](a: A, b: B) {}
def foo2[A, B](a: A, b: B)(implicit ev: B <:< A) {}
```

向以上方法传入参数

```scala
foo(peter, cat)
foo2(peter, cat)
```

结果第一行正常执行，因为 `Animal` 类型虽然与 `Person` 无关，但是编译器一旦发现 `B` 需要为 `A` 的子类时，就会将 `Animal` 类型提升为 `Any` 类型，此时自然 `Person` 是其子类，所以方法通过了检查。而第二行则直接报错，因为类型约束只会直接检查，不会自动进行类型转换。

#### =:=

`=:=` 用于限制参数类型必须为某一类型。

例

定义方法

```scala
def bar[A](a: A)(implicit ev: A =:= Person): Unit = {}
```

以上指定了类型 `A` 必须为 `Person` 类型。

调用该方法

```scala
//  正常调用
val peter = new Person("Peter")
bar(peter)

//  错误调用，Student 类型不是 Person 类型
val jane = new Student("Jane")
bar(jane)
```

可以看到 `=:=` 和 `<:<` 一样不会进行类型转换，不会认为子类就是父类。


### 类型参数边界

因为隐式操作的存在类型参数边界又多了几个，因此本节是对泛型一节中的类型参数边界的补充。

#### => 与 视图边界

`=>` 用于限定对于参数类型 A 来说，必须存在一个隐式转换能够将 A 转变为目标类型。

例

定义一个方法

```scala
 def limitView[A](a: Person)(implicit ev1: A => Person) {}
```

以上方法限定了上下文环境中必须存在一个转换函数能够将类型 `A` 转换为类型 `Person`。

```scala
val linda = new Teacher("Linda")
implicit def teacher2Person(t: Teacher): Person = new Person(t.name)
limitView(peter)
```

这种做法与视图边界(View Bound) 相同，不过视图边界早已经被废弃，但是还是有不少代码中使用了视图边界，所以还是需要知道以下，以下就是使用视图边界的写法：

```scala
def limitViewOld[A <% Person](a: Person) {}
```

#### 上下文边界

上下文边界用于限定对于类型 `[A: Bound]` 来说，必须存在一个隐式转换可以使类型 `[A: Bound]` 变为类型 `Bound[A]`。上下文边界实际是 `def f[A](a: A)(implicit ev: Bound[A])` 的语法糖。

例

定义一个方法

```scala
def append[A: ArrayBuffer](a: A): Unit = {
    var arrayBuffer = implicitly[ArrayBuffer[A]]
    arrayBuffer += a
}
```

其中 `implicitly` 用于从上下文环境中获得指定类型的隐式值。

调用该方法

```scala
implicit val persons = ArrayBuffer[Person]()
append(peter)
persons.foreach { p => println(p.name) }
```

以上的例子即表示对于类型 `Person` 来说，必须存在一个隐式转换能够将 `Person` 转换为 `ArrayBuffer[Person]`。
