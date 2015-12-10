---
comments: true
date: 2015-12-10T09:57:36+08:00
description: ""
draft: fasle
keywords:
- kotlin
slug: "JGSK-Kotlin-03-delegation"
tags:
- kotlin
title: "JGSK - Kotlin - 03.委托"
toc: true
topics:
- JGSK
---


### 类的委托

类的委托即一个类中定义的方法实际是调用另一个类的对象的方法来实现的。

<!--more-->

在 Java 中实现委托机制就像下面的例子一样，非常麻烦。

```java
class Base {
  public void echo(String message){
    System.out.println(message);
  }
}
//  委托类
class Delegation {
    private Base base;
    public Delegation(){
        this.base = new Base();
    }
    //  实际调用的是 Base 类的方法
    public void echo(String message){
        base.echo(message);
    }
}
```

而在 Kotlin 中只要被委托的类实现了接口，那么就可以很简单的建立一个拥有此接口所有公共实现方法的委托类。

创建接口和实现此接口的被委托的类

```kotlin
interface Base {
    fun echo(msg: String)
}

class BaseImpl(val prefix: String) : Base {
    override fun echo(msg: String) {
        println("$prefix $msg")
    }
}
```

建立委托类

```kotlin
class Derived(b: Base) : Base by b
```

以上 `Derived` 类继承了所有来自 `Base` 的方法，并且委托一个传入的 `Base` 类的对象来 执行这些方法。

使用委托类

```kotlin
val b = BaseImpl("Hello")
b.echo("World") //  Hello World
val d = Derived(b)  //  Hello World
d.echo("World")
```


### 属性的委托

属性的委托指的一个类的某个属性的值不是在类中直接进行定义，而是由某个类的方法来进行 setter 和 getter。默认属性委托都是线程安全的。属性委托适合那些属性的需要复杂的计算但是计算过程可以被重用的场合。

定义一个被委托的类

该类需要包含 `getValue()` 方法和 `setValue()` 方法，且参数 `thisRef` 为进行委托的类的对象，`prop` 为进行委托的属性的对象。

```kotlin
class Delegate {

    private var message = "Default Message"

    operator fun getValue(thisRef: Any?, prop: KProperty<*>): String {
        return "${prop.name} = $message from $thisRef"
    }

    operator fun setValue(thisRef: Any?, prop: KProperty<*>, value: String) {
        message = value
    }
}
```


定义包含属性委托的类

```kotlin
class Example {
    var msg: String by Delegate()
}
```

访问该属性

```kotlin
val e = Example()
println(e.msg)  //  msg = Default Message

e.msg = "New Message"
println(e.msg)  //  msg = New Message
```

在使用属性委托时，被委托的类的方法（即接收者）的返回值必须与委托的属性相同或者其父类。

### 标准委托

Kotlin 的标准库中已经内置了很多工厂方法来实现属性的委托。

#### Lazy

`lazy` 用于进行惰性加载，在 `JGSK - 34.Lazy` 章节中已经介绍过了。

#### Observable

`observable` 可以用于实现观察者模式。

定义包含被委托的属性的类

`Delegates.observable` 接收三个参数：包含委托的属性的元数据的 `KProperty` 对象，旧值，新值。

```kotlin
class User {
    var name: String by Delegates.observable("<init value>") {
        prop, old, new ->
        if (old != new) {
            println("$old -> $new")
        }
    }
}
```

访问该属性

```kotlin
val user = User()
user.name = "first" //  <init value> -> first
user.name = "first"
user.name = "second"    //  first -> second
```

也可以使用 `vetoable` 代替 `observable`，该方法拥有布尔类型的返回值，返回 `false` 的话可以取消对该属性的修改。

在以上 `User` 中定义一个新属性

```kotlin
var age: Int by Delegates.vetoable(0) {
    prop, old, new ->
    println("$old -> $new")
    if (new < 20) true else false
}
```

访问该属性

```kotlin
user.age = 10   //  0 -> 10
println(user.age)   //  10
user.age = 20   //  10 -> 20
println(user.age)   //  20
```

#### Not Null

`notNull` 适用于那些无法在初始化阶段就确定属性值的场合。

```kotlin
class Foo {
    var notNullBar: String by Delegates.notNull<String>()
}

foo.notNullBar = "bar"
println(foo.notNullBar)
```

需要注意，如果属性在赋值前就被访问的话则会抛出异常。

#### 以 Map 形式保存属性的值

Kotlin 中有一种特别的委托，可以以 Map 作为一个类的构造方法的参数，访问该类的属性就是访问该 Map 的键值对。这种做法非常类似 Groovy 中的带名构造方法。

要实现这一功能需要得意于 Kotlin 内置的属性的扩展方法 `kotlin.properties.getValue`

例

```kotlin
import kotlin.properties.getValue

class Person(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int by map
}
```

可以像普通类一样访问其各个属性

```kotlin
val person = Person(mapOf(
        "name" to "John",
        "age" to 25
))
println(person.name)
println(person.age)
```

对于可变值，可以使用 `MutableMap` 代替，并导入扩展方法 `kotlin.properties.setValue`

```kotlin
import kotlin.properties.setValue

class MutablePerson(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```

## 总结

- Kotlin 支持类的委托和属性的委托。
- 标准库中内置 `lazy`,`observable`,`vetoable` 等实现委托的工厂方法。
- 可以使用 map 委托来实现类似 Groovy 的默认构造方法

---

项目源码见 [JGSK/k03_delegate](https://github.com/SidneyXu/JGSK)
