---
comments: true
date: 2015-12-11T12:13:45+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-closure-java"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法 vsLambda 表达式 vs 闭包 - Java 篇"
toc: true
topics:
- JGSK
---



## Java 篇

### 方法

#### 定义方法

完整的 Java 定义语法为

<!--more-->

``` 
[访问控制符] [static] [返回值类型] 方法名(参数列表)
```

Java 中方法必须声明在类的内部，且被分为成员方法和静态方法。

成员方法表示类的对象的一种行为，声明时没有关键字 `static`。

``` java
public int add(int x, int y) {
    return x + y;
}
```

静态方法使用关键字 `static` 声明，属于类的行为，或称作类对象的行为，因此调用时无需创建任何对象。`main()` 方法就是最常见的静态方法。

``` java
public static void main(String[] args) {
}
```

#### Varargs

Varargs 即参数长度不确定，简称变参。Java 使用符号 `...` 表示变参，但是变参只能出现在参数列表的最后一个，即 `sum(int x, int y, int...n)` 是合法的，但 `sum(int x, int...n, int y)` 或 `sum(int...n, int x, int y)` 都是非法的。

声明一个变参方法

``` java
class Calculator {
    public void sum(int... n) {
        int result = 0;
        for (int i : n) {
            result += i;
        }
        System.out.println(result);
    }
}
```

调用该方法

``` java
Calculator calculator = new Calculator();
calculator.sum(1, 2, 3);
```

#### b

Java 不支持参数默认值，所以调用时必须为每一个参数赋值

``` java
private static void say(String name, String word) {
    if (word == null) {
        System.out.println(word + " " + name);
    }
}

say("Peter", null);
```

#### 返回值

Java 中方法除非返回值类型声明为 `void` 表示没有返回值，否则必须在方法中调用 `return` 语句返回到调用处。

``` java
public int add(int x, int y) {
    return x + y;
}
```

### Lambda 表达式

所谓的 Lambda 表达式其实就是一个匿名函数。Java 中由于不能有独立于类的函数存在，所以匿名函数一直都是通过定义一个包含抽象方法的匿名内部类来实现的。而 Java 1.8 后引入的 Lambda 表达式其实只是原来实现方式的一种语法糖。

Java 1.8 以前使用匿名内部类的代码

``` java
  button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(final ActionEvent e) {
        System.out.println("Perform Click");
    }
});
```

Java 1.8 使用 Lambda 的代码

``` java
button.addActionListener(e -> System.out.println("Perform Click"));
```

#### SAM 类型与函数接口

SAM （Single Abstract Method）是有且仅有一个抽象方法的类型，该类型可以是抽象类也可以是接口。

函数接口是 Java 1.8 中引入的概念，其实就是一个普通的接口，但是该接口中有且仅有一个抽象方法。所以函数接口就是一种  SAM 类型。

Java 中的 Lambda 表达式就是通过函数接口来实现的，所以其与 1.8 以前使用匿名内部类的最大区别就是匿名内部类中可以定义多个抽象方法，而要使用 Lambda 表达式则只能定义一个抽象方法。

定义一个函数接口

``` java
@FunctionalInterface
interface Excite {
    String accept(String from);
}
```

以上使用了注解 `@FunctionalInterface`，在 Java 1.8 的初期版本这个注解用于标示一个接口为函数接口，但在现在的版本已经可以省略这个注解了。

#### 使用 Lambda 表达式

Lambda 表达式的基本语法为

``` java
(参数列表) -> {执行语句}
```

如果执行语句只有一句的话可以省略包裹其的花括号

例

``` java
Excite excite = (word) -> word + "!!";
```

然后我们可以很方便的调用这个接口

``` java
excite.accept("Java")
```

如果 Lambda 语句只有一个语句且只有一个参数，且该语句调用的是一个静态方法，则可以使用符号 `::` 进一步缩减代码

``` java
Excite hello = (w) -> String.valueOf(w);
```

以上等同于

``` java
Excite hello = String::valueOf;
```

如果 Lambda 语句只有一个语句，且该语句为使用类的无参构造方法创建类的实例，则也可以使用符号 `::` 进一步缩减代码

``` java
Excite hello = (w) -> new Word();
```

以上等同于

``` java
Excite hello = Word::new;
```



#### 多个参数

函数接口也可以接收多个参数，这些参数可以为泛型而不是具体类型，实际上使用泛型的函数接口更为常见

以下定义了一个接收两个参数 `F1` 和 `F2`，返回 `T` 类型的接口

``` java
interface Convert<F1, F2, T> {
    T convert(F1 from1, F2 from2);
}
```

使用该接口

``` java
Convert<Integer, Integer, String> convert = (x, y) -> {
    int result = x + y;
    return x + " plus " + y + " is " + result;
};
System.out.println(convert.convert(1, 2));  //  1 plus 2 is 3
```

#### 变参

在 Lambda 表达式中也一样可以使用变参

例

定义一个含有变参的接口

``` java
interface Contact<F, T> {
    T accept(F... from);
}
```

使用该接口

``` java
Contact<String, String> contact = (args) -> String.join(",", args);
contact.accept("Java", "Groovy", "Scala", "Kotlin");
```

#### 内置函数接口

通过以上例子我们可以看到要想使用 Lambda 表达式我们必须先定义一个函数接口，这样用法太过麻烦。所以 Java 提供了一些内置的函数接口供我们调用.

##### Predicate

Predicate 接口用于接收一个参数并返回 Boolean 值，主要用于处理逻辑动词。该接口还有一个默认方法 `negate()` 用于进行逻辑取反。

``` java
Predicate<String> predicate = (s) -> s.length() > 0;
assert predicate.test("foo");
assert !predicate.negate().test("foo");
```

##### Function

Function 接口接收一个参数并返回单一结果，主要用于进行类型转换等功能。该接口也提供了一个 `andThen()` 方法用于执行链式操作。

``` java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);
assert toInteger.apply("123") == 123;
assert backToString.apply("123").equals("123");
```

##### Supplier

Supplier 接口没有参数，但是会返回单一结果，可以用于实现工厂方法。

``` java
Supplier<Calculator> calculatorSupplier = Calculator::new;
assert calculatorSupplier.get().add(1, 2) == 3;
```

##### Consumer

Consumer 接口接收一个参数，没有返回值，用于对传入的参数进行某些处理。该接口也提供了 `andThen()` 方法。

``` java
Consumer<Person> calculatorConsumer = (p) ->
        System.out.println("The name is " + p.getName());
calculatorConsumer.accept(new Person("Peter"));
```

##### Comparator

Comparator 接口接收两个参数，返回 int 值，用于进行排序操作。该接口提供了 `reversed()` 方法进行反序排列。

``` java
Comparator<Person> comparator = (p1, p2) ->
        p1.getAge().compareTo(p2.getAge());
Person john = new Person("John", 20);
Person alice = new Person("Alice", 18);

assert comparator.compare(john, alice) > 0;
assert comparator.reversed().compare(john, alice) < 0;
```

#### 函数接口作为参数

函数接口也可以作为参数来使用

``` java
private static int max(int[] arr, Function<int[], Integer> integerFunction) {
    return integerFunction.apply(arr);
}
```

使用该接口

``` java
int maxValue = max(new int[]{3, 10, 2, 40}, (s) -> {
    int max = -1;
    for (int n : s) {
        if (max < n) max = n;
    }
    return max;
});
assert maxValue == 40;
```



### 闭包

#### 概念

闭包是一种带有自由变量的代码块，其最显著的特性就是能够扩大局部变量的生命周期。

#### 闭包与 Lambda 表达式

闭包与 Lambda 表达式的概念非常容易让人混淆，但两者确实是不同的东西。Lambda 表达式是匿名函数，而闭包则是实现函数是第一类对象的一种手段。两者的直接关系是 Lambda 表达式可以作为闭包的一种表现形式，但闭包除了 Lambda 表达式也可以表现为多种形式，这也就是在各种语言中闭包的写法会有很多差别的原因。

#### 闭包与方法

闭包和方法的最大区别是方法执行完毕后其内部的变量便会被释放，而闭包不会。闭包可以进行嵌套，而方法不行。

#### Java 中的闭包

Java 中和闭包相近的概念就是匿名类以及本章所说的 Lambda 表达式。但是这两种都不是真正意义上的闭包。

先看一个例子

``` java
interface Excite {
    String accept(String from);
}
private static Excite excite(String s) {
  Excite e = from -> {
    from = "from=" + from;
    return from + "," + s;
  };
  return e;
}
Excite excite = excite("hello");
System.out.println(excite.accept("world")); //  from=world,hello
```

以上例子中 `e` 为 Lambda 表达式，其定义在 `excite()` 方法中并且访问了该方法的参数列表。按照生命周期，`excite()` 的参数 `s` 应该在方法被调用后就自动释放，即 `Excite excite = excite("hello")` 调用后就不存在参数 `hello` 了，但实际打印语句还是打印出来了。

这一表现形式非常像闭包，因为参数明显在其生命周期外还存活。但是如果我们在 Lambda 表达式内试图修改参数 `s` 的值后编译器会报 `s` 必须为 `final` ，这就是说该变量实际并不是自由变量，所以并不是真正的闭包。

如果查看 Groovy 的闭包形式你可以发现 Groovy 实际也是通过实现继承自 `Closure` 类的匿名内部类来实现闭包形式的，这一点与 Java 一致。所以理论上 Java 也能实现真正的闭包，至于 1.8 为什么没有这么做就不得而知了。


