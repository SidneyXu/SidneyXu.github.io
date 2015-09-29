---
comments: true
date: 2015-09-22T12:25:45+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-19-inherit"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 19.继承，抽象类与接口"
toc: true
topics:
- JGSK
---


## Java 篇

### 抽象类

- 抽象类是使用关键字 `abstract` 声明的类。抽象类与普通类的最大区别是抽象类不能被实例化。
- 抽象类可以拥有没有方法体的抽象方法也可以拥有普通方法，抽象方法使用 `abstract` 声明。
- 普通类可以使用关键字 `extends` 继承普通类，继承时必须重写所有抽象方法。
- 普通类可以继承抽象类，抽象类可以继承抽象类。

<!--more-->

定义一个抽象类

```java
public abstract class View {

    protected int width;
    protected int height;

    public abstract String getDescription();
}
```

继承抽象类

```java
public class Button extends View {

    private String text;

    public Button(String text, int width, int height) {
        this.text = text;
        this.width = width;
        this.height = height;
    }

    @Override
    public String getDescription() {
        return text;
    }
}
```

### 接口

#### 基本概念

- 接口使用关键字 `interface` 声明，接口中声明的所有普通方法都为 `public abstract`，所有属性都为 `public static final` 常量。
- 类使用关键字 `implements` 实现接口
- 一个接口可以继承其它接口
- Java 8 开始支持函数式接口和接口默认方法

#### 使用接口

定义一个接口

```java
public interface OnClickListener {

    void onClick();

    String getDescription();
}
```

实现接口

```java
public class Button extends View implements OnClickListener {

    private String text;

    public Button(String text, int width, int height) {
        this.text = text;
        this.width = width;
        this.height = height;
    }

    @Override
    public void onClick() {
        System.out.println("Click Button " + text);
    }

    @Override
    public String getDescription() {
        return text;
    }
}
```

#### 函数式接口

函数式接口有且只有一个抽象方法，是实现 Lambda 表达式的手段，具体见方法与闭包章节。

```java
interface Convert<F, T> {
    T convert(F from);
}
```

#### 接口默认方法

Java 1.8 新增功能，以前版本的接口只能有抽象方法，现在则可以为接口提供默认方法，但是默认方法无法在 Lambda 表达式内部被访问

```java
interface Calculator {
    int add(int x, int y);

    //  接口默认方法
    default int addOne(int x) {
        return x + 1;
    }
}
```

## Groovy 篇

### 抽象类

Groovy 中抽象类的使用类似 Java。

定义一个抽象类

```groovy
abstract class View {

    protected def width
    protected def height

    abstract def getDescription()
}
```

继承抽象类

```groovy
class Button extends View {

    private def text

    @Override
    def getDescription() {
        return text
    }
}
```

### 接口

Groovy 中接口使用类似 Java，但是没有 Java 1.8 那些特性

定义一个接口

```groovy
interface OnClickListener {

    def onClick()

    def getDescription()
}
```

实现接口

```groovy
class Button extends View implements OnClickListener {

    private def text

    @Override
    def onClick() {
        println("Click Button $text")
    }

    @Override
    def getDescription() {
        return text
    }
}
```

### 使用闭包实现接口和抽象类

Groovy 中可以通过定义一个包含需要实现的类或接口的方法的闭包来实现抽象类或接口。

例

```groovy
def textview = [getDescription: { -> }] as View
textview.metaClass.getDescription = { -> "This is a TextView." }
println(textview.getDescription())
```

以上例子通过定义了一个包含 `getDescription()` 方法闭包来实现抽象类 `View`。

同样该方法也适用于接口

```groovy
def listener = [onClick: { -> println("Trigger click event.") }] as OnClickListener
listener.onClick()
```

## Scala 篇

### 抽象类

- Scala 中抽象类的使用类似 Java，但是声明抽象方法时无需加上 `abstract` 关键字。
- 实现抽象方法时可以不加关键字 `override`，重新普通方法时则必须加 `override`

定义一个抽象类

```scala
abstract class View(val width: Int, val height: Int) {

  def getDescription(): String
}
```

继承抽象类

其中 `getDescription()` 实现了 `View` 中的抽象方法，所以可以不加 `override`
，而 `toString` 则重写了普通方法，所以必须加 `override`。

```scala
class Button(val text: String, override val width: Int, override val height: Int) extends View(width, height) {
  def getDescription(): String = text
  override def toString: String = getDescription()
}
```

### 接口

Scala 中没有接口的概念，但是有更加强大的 Trait，在后面的章节会具体说。

### 属性重写规则

Scala 中不但可以重写方法，也可以重写属性，属性重写需要遵守以下规则：

- `def` 可以重写 `def`
- `val` 可以重写 `val` 或无参的 `def`
- `var` 只能重写抽象的 `var`

```scala
class Person(val name: String) {
  val valValue = "PersonVal"
  var varValue = "PersonVar"

  def defValue1 = "PersonDef"
  def defValue2 = "PersonDef2"
}

class Employee(name: String, salary: Int) extends Person(name) {
  override val valValue: String = "val Override val"
  //  Wrong!!
  //  override var x: String = "var Override var"
  override val defValue1: String = "val Override def"
  override def defValue2: String = "def Override def"
}

def employee = new Employee("Jane", 30)
println(employee.valValue)  //  val Override val
println(employee.varValue)  //  PersonVar
println(employee.defValue1) //  val Override def
println(employee.defValue2) //  def Override def
```

### Protected

- Scala 中 `protected` 修饰的成员可以被子类访问，但是不能被其它位置的类（包括同包的其它类）访问。而 Java 则是可以被同包及子类访问。
- 如果要让同包的其它类访问，需要使用包修饰符 `protected[packageName]`。
- `protected[this]` 可以将访问权限限定在当前对象

```scala
//限定子类
protected def info = name

//限定子类 + 同包的类
protected[_16_inherit] def info2 = name
```

## Kotlin 篇

### Open Class

- 与其它三门语言都不一样，Kotlin 中默认所有 Class 都是 final 的，即不能被任何类继承。
- 如果希望一个类能够被继承，需要将该类声明为 `open`。同样，Kotlin 中所有方法也为 final，如果希望方法能被重写，也必须声明方法为 `open`，且重写的方法必须声明 `override` 关键字。
- Kotlin 中使用符号 `:` 来继承类或实现接口

声明一个 open class

```kotlin
open class Person(name: String) {
    open fun foo() {
    }
    open fun bar() {
    }
    fun foo2(){
    }
}
```

继承该 open class

```kotlin
class Employee(name: String) : Person(name) {
    constructor(name: String, age: Int) : this(name) {
    }
    override fun foo() {
        super.foo()
    }
    final override fun bar() {
        super.bar()
    }
    override fun toString(): String {
        return super.toString() + javaClass.getName()
    }
}
```

### 抽象类

Kotlin 中抽象类的声明类似 Java。但是抽象类和其包含抽象方法默认为 `open`，而普通方法则仍然默认为 `final`。

定义一个抽象类

```kotlin
abstract class View(val width: Int, val height: Int) {
    abstract fun getDescription(): String

    open fun onClick() {
        println("Click event of View")
    }
}
```

继承抽象类

```kotlin
class Button(val text: String, width: Int, height: Int) : View(width, height) {
    override fun getDescription(): String {
        return text
    }
    override fun onClick() {
    }
}
```

### 接口

- Kotlin 中接口和接口中的所有成员都默认为 `open`。
- Kotlin 中接口除了有抽象方法也可以有普通方法和抽象属性

定义一个接口

```kotlin
interface OnClickListener {
    //  抽象属性
    val prop: Int

    //  普通方法
    fun onClick() {
        println("Click event of OnClickListener")
    }

    //  抽象方法
    fun getDescription(): String
}
```

实现接口

```kotlin

class Button(val text: String, width: Int, height: Int) : View(width, height), OnClickListener {

    override fun onClick() {
        super<View>.onClick()
        super<OnClickListener>.onClick()
    }

    override val prop: Int
        get() = width * height

    override fun getDescription(): String {
        return text
    }
}
```

以上属性 `prop` 实现了 `OnClickListener` 接口的抽象属性。而在 `onClick()` 方法中可以通过 `super<接口或类名>.方法名` 来指定调用的父类方法的具体源头。




### 使用闭包实现接口和抽象类

Groovy 中可以通过定义一个包含需要实现的类或接口的方法的闭包来实现抽象类或接口。

例

```groovy
def textview = [getDescription: { -> }] as View
textview.metaClass.getDescription = { -> "This is a TextView." }
println(textview.getDescription())
```

以上例子通过定义了一个包含 `getDescription()` 方法闭包来实现抽象类 `View`。

同样该方法也适用于接口

```groovy
def listener = [onClick: { -> println("Trigger click event.") }] as OnClickListener
listener.onClick()
```


## 总结

- Kotlin 使用符号 `:` 而非 `extends` 继承类和接口
- Scala 没有接口的概念
- Kotlin 默认普通方法和类都是 `final` 的
- Groovy 中可以通过闭包继承类或接口


---

项目源码见 [JGSK/_19_inherit](https://github.com/SidneyXu/JGSK)
