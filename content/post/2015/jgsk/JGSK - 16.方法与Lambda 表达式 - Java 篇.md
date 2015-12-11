---
comments: true
date: 2015-12-11T12:13:45+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-lambda-java"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 16.方法与Lambda 表达式 - Java 篇"
toc: true
topics:
- JGSK
---


## Java 篇

### 方法

#### 定义方法

完整的 Java 定义语法为

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

#### 参数默认值

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

Lambda 表达式是 Java 1.8 新提供的功能。尽管它有些看起来像闭包，但是 Lambda 表达式并不是闭包，它的功能也比闭包弱很多。Java 1.8 之所以提供 Lambda 表达式是因为原来 Java 繁琐的语法已经跟不上现在的潮流了，而之所以没有把闭包作为 1.8 的功能很可能是为了兼容以前的 Java 程序。

Java 1.8 以前的代码

``` java
  button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(final ActionEvent e) {
        System.out.println("Perform Click");
    }
});
```

Java 1.8 的代码

``` java
button.addActionListener(e -> System.out.println("Perform Click"));
```

#### 函数接口

Java 中的 Lambda 表达式主要是通过函数接口来实现的。所谓的函数接口就是普通的接口，但是该接口里有且仅有一个方法。实现该接口的具体方法可以被改写成 Lambda 语句。

定义一个函数接口

``` java
@FunctionalInterface
interface Excite {
    String accept(String from);
}
```

以上使用了注解 `@FunctionalInterface`，在 Java 1.8 的初期版本这个注解用于标示一个接口为函数接口，但在现在的版本已经可以省略这个注解了。

#### 使用 Lamda 表达式

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

在 Lambda 表达式中也一样可以使用变参，具体例子如下

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

Predicate 接口用于接收一个参数并返回 Boolean 值，主要用于处理逻辑动词。该接口还有一个默认方法 `negate()` 用于进行逻辑取反。（Java 1.8 以前接口不能定义默认行为，相关内容会在接口那一章谈到）

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
