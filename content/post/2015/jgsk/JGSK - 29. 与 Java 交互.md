---
comments: true
date: 2015-10-20T07:00:27+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-29-与 Java 交互"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 29. 与 Java 交互
toc: true
topics:
- JGSK
---

## 共通篇

Groovy，Scala 和 Kotlin 都是 JVM 上的语言，在设计之初就考虑到了与 Java 的兼容性，所以这三门语言几乎都能无缝调用 Java 代码，因此也能很简单地实用现在众多成熟的 Java 类库。而 Java 调用这三门语言也不是太麻烦，所以可以根据实用场景在这四门语言中进行便捷地切换。

<!--more-->

## Groovy

### Groovy 调用 Java

Groovy 调用 Java 就像 Java 调用 Java 一样没有任何其它操作。

定义一个 Java 类 `JavaBean.java`

```java
public class JavaBean {

    private String name;

    public JavaBean(String name) {
        this.name = name;
    }

    public int calc(int x, int y) {
        return x + y;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public static void hello(JavaBean bean) {
        System.out.println("Hello, this is " + bean.name);
    }
}
```

编写一个 Groovy 类调用以上的 Java Bean

```groovy
class GroovyCallJava {

    static void main(args) {
        JavaBean javaBean = new JavaBean("JavaBean")
        println javaBean.getName()  //  JavaBean
        println javaBean.calc(2, 3) //  5
        JavaBean.hello(javaBean)    //  Hello, this is JavaBean
    }
}
```

### Java 调用 Groovy

编写一个 Groovy 类 `GroovyBean.groovy`

```groovy
class GroovyBean {

    def name

    GroovyBean(name) {
        this.name = name
    }


    def calc(x, y) {
        x + y
    }

    static def hello(GroovyBean bean) {
        println("Hello, this is ${bean.name}")
    }
}
```

编写调用以上 Groovy 代码的 Java 类

```java
public class JavaCallGroovy {

    public static void main(String[] args) {
        GroovyBean groovyBean = new GroovyBean("GroovyBean");
        System.out.println(groovyBean.getName());   //  GroovyBean
        System.out.println(groovyBean.calc(2, 3));  //  5
        GroovyBean.hello(groovyBean);               //  Hello, this is GroovyBean
    }
}
```

## Scala

### Scala 调用 Java

Scala 调用 Java 也非常简单

定义一个 Java 类 `JavaBean.java`

```java
public class JavaBean {

    private String name;

    public JavaBean(String name) {
        this.name = name;
    }

    public int calc(int x, int y) {
        return x + y;
    }

    public String getName() {
        return name;
    }

    public static void hello(JavaBean bean) {
        System.out.println("Hello, this is " + bean.name);
    }

}
```

编写一个 Scala 类调用以上的 Java Bean

```Scala
object ScalaCallJava extends App {

  val javaBean = new JavaBean("JavaBean")
  println(javaBean.getName) //  JavaBean
  println(javaBean.calc(2, 3)) //  5

  JavaBean.hello(javaBean) //  Hello, this is JavaBean
}
```

### Java 调用 Scala

Java 调用 Scala 时需要注意 `class` 和 `object` 的区别。此外在 Scala 对象中如果属性没有声明为 `@BeanProperty` 的话，调用时需要使用 `对象.属性名()` 来调用，声明后才可以使用 Java 风格的 `对象.get属性名()` 来调用。

编写一个 Scala 类 `ScalaBean.scala`

```scala
class ScalaBean(@BeanProperty val name: String) {

  val age: Int = 10

  def calc(x: Int, y: Int) = x + y
}

object ScalaBean {

  def hello(bean: ScalaBean): Unit = println(s"Hello, this is ${bean.name}")
}

object ScalaUtils {
  def foo = println("Foo...")
}
```

编写调用以上 Scala 代码的 Java 类

```java
public class JavaCallScala {

    public static void main(String[] args) {
        ScalaBean scalaBean = new ScalaBean("ScalaBean");
        // Scala 属性的默认调用方式
        System.out.println(scalaBean.name());       //  ScalaBean
        // 声明为 @BeanProperty 后提供的调用方式
        System.out.println(scalaBean.getName());    //  ScalaBean
        System.out.println(scalaBean.age());        //  10
        System.out.println(scalaBean.calc(2, 3));    //  5

        // 调用 Scala的单例对象，本质上调用的是下面一行
        ScalaBean.hello(scalaBean);                 //  Hello, this is ScalaBean
        ScalaBean$.MODULE$.hello(scalaBean);        //  Hello, this is ScalaBean

        ScalaUtils.foo();   //  Foo...
    }
}
```


## Kotlin

### Kotlin 调用 Java

Kotlin 调用 Java 也非常简单，但是如果 Java 的方法名是类似 `is` 这种在 Kotlin 是关键字，在 Java 中只是普通字符的场合，Kotlin 中调用时需要使用 "``" 括起来。

定义一个 Java 类 `JavaBean.java`

```java
public class JavaBean {

    private String name;

    public JavaBean(String name) {
        this.name = name;
    }

    public int calc(int x, int y) {
        return x + y;
    }

    public boolean is(String name) {
        return this.name.equals(name);
    }

    public String getName() {
        return name;
    }

    public static void hello(JavaBean bean) {
        System.out.println("Hello, this is " + bean.name);
    }
}
```

编写一个 Kotlin 类调用以上的 Java Bean

```kotlin
fun main(args: Array<String>) {
    val javaBean = JavaBean("JavaBean")
    println(javaBean.getName())     //  JavaBean
    println(javaBean.calc(2, 3))    //  5

    JavaBean.hello(javaBean)        //  Hello, this is Peter

    //  Escaping for Java identifiers that are keywords in Kotlin
    println(javaBean.`is`("Peter")) //  true
}
```

这种使用方式可以让 Java 享受到 Kotlin 优雅的空值处理方式

```kotlin
val list = ArrayList<JavaBean>()
list.add(javaBean)
val nullable: JavaBean? = list.get(0)
val notNull: JavaBean = list.get(0)
nullable?.getName()
notNull.getName()
```

### Java 调用 Kotlin

Java 调用 Kotlin 有几点比较特殊的，接下来一一说明。

#### Class 与 Object

编写一个 Kotlin 类 `ScalaBean.scala`

```scala
class KotlinBean(val name: String) {

    fun calc(x: Int, y: Int): Int {
        return x + y
    }

    companion object {
         @JvmStatic fun hello(bean: KotlinBean) {
            println("Hello, this is ${bean.name}")
        }

        fun echo(msg: String, bean: KotlinBean) {
            println("$msg, this is ${bean.name}")
        }
    }
}

object KotlinUtils {
     @JvmStatic fun foo() {
        println("Foo...")
    }

    fun bar() {
        println("Bar...")
    }
}
```

编写调用以上 Kotlin 代码的 Java 类

```java
public class JavaCallKotlin {

    public static void main(String[] args) {
        //  Class
        KotlinBean kotlinBean = new KotlinBean("Peter");
        System.out.printf(kotlinBean.getName());    //  Peter
        System.out.println(kotlinBean.calc(2, 3));  //  5

        KotlinBean.hello(kotlinBean);               //  Hello, this is Peter
        KotlinBean.Companion.echo("GoodBye", kotlinBean);   //  GoodBye, this is Peter

        //  Object
        KotlinUtils.foo();
        KotlinUtils.INSTANCE.bar();
    }
}
```

以上示例中使用了 `JvmStatic` 注解，该注解用于生成静态方法，如果没有使用该注解的话就必须使用隐式的单例对象 `INSTANCE$` 来调用 `object` 中的方法。这种处理方式与 Scala 非常相似，只是 Scala 是自动生成的罢了。

#### fun

Kotlin 中方法是可以脱离类而存在的，而在 Java 中这种方式是不允许的。定义这种方法时实际上 Kotlin 是产生了一个与包名相同的类来存储这些方法，所以 Java 中也需要使用包名调用这些方法。

定义一个脱离类的 Kotlin 的方法

```kotlin
package com.bookislife.jgsk.kotlin._29_java
fun foobar() {
    println("A function without class.")
}
```

在 Java 中调用以上方法

```java
_29_javaPackage.foobar();
```

#### 检查异常

Kotlin 不存在检查异常，但是 Java 中却到处都是检查异常，如果想抛出检查异常需要使用 `@throws(exceptionClassName::class)` 的语法。

Kotlin 代码

```kotlin
@Throws(IOException::class) fun declaredThrowAnException() {
    throw IOException()
}
```

Java 代码

```java
try {
    _29_javaPackage.declaredThrowAnException();
} catch (IOException ignored) {
}
```

#### 重载

Kotlin 拥有方法默认值和带名参数的特点，所以只需要定义一个包含所有参数的方法就可以满足需求。而 Java 没有这一特性，所以需要定义多个参数列表不同的同名方法，即重载才能满足需求。在 Kotlin 中可以直接使用 `jvmOverloads` 注解自动生成这些重载方法来让 Java 进行调用。

Kotlin 代码

```kotlin
@JvmOverloads fun f(a: String, b: Int = 0, c: String = "c") {
    println("a=$a b=$b c=$c")
}
```

Java 代码

```java
_29_javaPackage.f("x");             //  a=x b=0 c=c
_29_javaPackage.f("x", 10);         //  a=x b=10 c=c
_29_javaPackage.f("x", 10, "z");    //  a=x b=10 c=z
```

## 总结

- 三种语言都能比较简单地与 Java 互相调用。


---

项目源码见 [JGSK/_29_java](https://github.com/SidneyXu/JGSK)
