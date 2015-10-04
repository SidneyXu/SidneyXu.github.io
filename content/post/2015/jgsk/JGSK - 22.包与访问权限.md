---
comments: true
date: 2015-09-29T09:53:51+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-22-package"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 22.包与访问权限"
toc: true
topics:
- JGSK
---


## Java 篇

### 包

#### 创建包

- Java 使用关键字 `package` 来声明一个包。
- 包名中使用 `.` 作为路径分隔符，且包路径必须与源文件路径相同。
- 包只能声明在源文件开头。

```java
package a.b;
class B {}
```

<!--more-->

#### 导入包

- Java 使用关键字 `import` 导入包。
- 可以使用通配符 `*`，导入一个包下的所有类。
- 如果一个类中都是静态成员的话，可以使用静态导入 `static import` 导入该类的所有静态成员，这样使用时就不用指定类名了。
- 导入语句只能声明在源文件开头，包声明之后。

导入指定的类

```java
import a.b.B;
```

导入指定包下的所有类

```java
import a.b.*;
```

静态导入

```java
static import a.b.B.*;
```

#### package-info

Java 中每个包都可以声明一个名为 `package-info.java` 的源文件。该源文件主要在生成文档时用于描述当前包的功能和作用，有时也可以用于定义仅在包中共享的静态变量和静态方法。

### 访问权限

Java 中访问权限分为四种：

- **public**
	- 适用于类，属性，方法
	- public 表示总是可见。如果用于修饰类，则一个源文件中只能有一个类声明为 public。
- **private**
	- 适用于内部类，属性，方法
	- private 表示只在声明范围内可见，所以修饰类时只能修饰内部类。
- **friendly**
	- 适用于类，属性，方法
	- 默认访问控制权限，又称为 default，在同包中可见，使用时不用真的添加 `friendly` 或 `default` 关键字。
- **protected**
	- 适用于类，属性，方法
	- protected 等于 friendly + 子类可见。

>注意： Java 并没有父包和子包的概念，即 `a.b.c` 和 `a.b` 没有任何关系，也不存在访问控制权限的传递，所以 `a.b.c` 中定义的类无法访问 `a.b` 中的类定义的私有成员。

## Groovy 篇


### 包

#### 创建包

与 Java 相同。

#### 导入包

与 Java 相同。

### 访问权限

Groovy 中访问权限分为四种：

- **public**
	- 适用于类，属性，方法
	- 默认访问控制权限，可以省略，表示总是可见。和 Java 不一样，Groovy 中一个源文件中可以有多个类声明为 public。
- **private**
	- 适用于内部类，属性，方法
	- private 表示只在声明范围内可见，所以修饰类时只能修饰内部类。
- **protected**
	- 适用于类，属性，方法
	- protected 等于 friendly + 子类可见。
- **package**
	- 适用于类，属性，方法
	- 类似 Java 的默认权限，使用方式较为复杂，并不推荐。

    package 权限的例子

    定义一个包含 package 权限方法的类

    ```groovy
    package a.b
    class B {
      def foo() {}
      @PackageScope def packageFoo() {}
    }
    ```

    使用以上类

    ```groovy
    package a
    class A {

      @CompileStatic
      def foo() {
        def b = new B()(
        b.foo()
        b.packageFoo()
      }
    }
    ```

    调用包含 package 权限的类的方法必须被声明为 `@CompileStatic`，否则 package 权限会被忽略。


>Groovy 中也没有父包和子包的概念。


## Scala 篇


### 包

#### 创建包

- Scala 也使用关键字 `package` 来声明一个包。
- 包名中使用 `.` 作为路径分隔符，和 Java 不同包路径无须与源文件路径相同。
- 包可以声明在任意位置。

使用块声明包

``` scala
package A {
  package B {
    class B {
    }
  }
}
```

此时 `A` 和 `A.B` 包的成员对 `class C` 直接可见。

使用串联式包名

``` scala
package A.B.C {
  class C {
  }
}
```

此时 `A` 和 `A.B` 包的成员对 `class C` 并不直接可见。

使用顶部标记声明在源文件开头，对整个源文件有效

``` scala
package A.B
package C
```

等同于

``` scala
package A {
  package B {
    package C {}
  }
}
```

#### 导入包

- Scala 使用关键字 `import` 导入包。
- 可以使用通配符 `_`，导入一个包下的所有类
- 可以使用选取器 `{}` 导入一个包下的某些类或者指定别名
- 导入语句可以放在任意位置，包括方法内部。

导入指定的类

```scala
import a.b.B
```

导入指定包下的所有类

```scala
import a.b._
```

使用选取器导入指定的类

```scala
import java.awt.{Color,Font}
```

使用选取器重命名选取的类

```scala
import java.util.{HashMap=>JavaHashMap}
```

#### 包对象

- 包对象用于组织包内可见的常量和方法，而不用具体包含在某个 utils 对象中，通常这是更好的做法。
- 每个包都可以包含一个包对象
- 包对象会被编译为对应包内的 `package.class` 类，所以通常做法是在对应包内建立一个名为 `package.scala` 的类来管理包对象。

``` scala
package object Constants {
  val VERSION = "0.0.1"
}
```

### 访问权限

Scala 中 的访问权限比较复杂

- **public**
	- 适用于类，属性，方法
	- 默认访问控制符，总是可见。一个源文件中可以包含多个 public 的类。
- **private**
	- 适用于内部类，属性，方法
	- private 表示只在当前声明范围内可见，所以修饰类时只能修饰内部类。与 Java 最大的不同是外部类是无法调用内部类的 private 成员的。
- **protected**
	- 适用于类，属性，方法
	- protected 表示仅子类可见，与 Java 完全不同。
- **scope**
	- Scala 中可以结合以上的访问控制符和scope符号 `[]` 来自定义访问控制权限。

    自定义包作用域

    ``` scala
    package A.B.C {
      class C {
        private[C] def desc = "desc"		//同包
        private[B] def impress = "impress"	//上一级包
      }
    }
    ```


> Scala 包支持嵌套，即下层可以直接访问上层作用域的成员。但是如果使用了串联式包名，则必须手动引入对应的包才能正常使用。而在 Java 中不同包名没有任何直接关系。


## Kotlin 篇


### 包

#### 创建包

与 Java 相同。

#### 导入包

与 Java 相同。但是 Kotlin 在导入包时可以指定别名。

指定别名

``` kotlin
import foo.bar as b

val b = b()
b.bar()
```

### 默认包

如果一个 kotlin 源文件中没有任何包声明，则其当中的代码均属于默认包，导入时包名即为函数名

``` kotlin
fun hello() {
    println("hello, Default Package")
}

import hello
hello()
```

### 访问权限

 Kotlin 中访问权限分为四种：

- **public**
	- 适用于类，属性，方法
	- public 表示总是可见。
- **private**
	- 适用于类，属性，方法
	- private 表示在声明范围及同模块的子作用域内可见。
- **internal**
	- 适用于类，属性，方法
	- 默认作用域，同模块中可见。
- **protected**
	- 适用于类，属性，方法
	- protected 表示 private + 子类可见，与 Java 完全不同。

>模块是 Kotlin 的一个特殊的概念，表示的是一个单独的编译单元，概念类似Maven中的模块。


## 总结

- Scala 的包声明与其它语言都不一样
- Scala 和 Kotlin 导入包时支持指定别名
- Scala 和 Kotlin 的访问权限与另两种有很大区别


---

项目源码见 [JGSK/_22_package](https://github.com/SidneyXu/JGSK)
