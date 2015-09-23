---
comments: true
date: 2015-09-20T20:46:05+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-16-method-and-closure"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 16.方法,Lambda 表达式与闭包
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

Java 中方法必须声明在类的内部，且被分为成员方法和静态方法

成员方法表示类的对象的一种行为，声明时没有关键字 `static`

```java
public int add(int x, int y) {
    return x + y;
}
```

静态方法使用关键字 `static` 声明，属于类的行为，或称作类对象的行为，因此调用时无需创建任何对象。`main()` 方法就是最常见的静态方法。

```java
public static void main(String[] args) {
}
```

#### Varargs

Java 使用 `...` 表示变参，但是变参只能出现在方法参数的最后一个

声明一个变参方法

```java
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

```java
Calculator calculator = new Calculator();
calculator.sum(1, 2, 3);
```

#### 方法的默认值

Java 不支持方法默认值，所以调用时必须为每一个参数赋值

```java
private static void say(String name, String word) {
    if (word == null) {
        System.out.println(word + " " + name);
    }
}

say("Peter", null);
```

#### 返回值

Java 中方法除非返回值类型声明为 `void`，否则必须在方法中调用 `return` 语句返回到调用处.

```java
public int add(int x, int y) {
    return x + y;
}
```

### Lambda 表达式

Lambda 表达式是 Java 1.8 新提供的功能。尽管它有些看起来像闭包，但是 Lambda 表达式并不是闭包，它的功能也比闭包弱很多。Java 1.8 之所以提供 Lambda 表达式是因为原来 Java 繁琐的语法已经跟不上现在的潮流了，而之所以没有把闭包作为 1.8 的功能很可能是为了兼容以前的 Java 程序。

Java 1.8 以前的代码

```java
  button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(final ActionEvent e) {
        System.out.println("Perform Click");
    }
});
```

Java 1.8 的代码

```java
button.addActionListener(e -> System.out.println("Perform Click"));
```

#### 函数接口

Java 中的 Lambda 表达式主要是通过函数接口来实现的。所谓的函数接口就是普通的接口，但是该接口里有且仅有一个方法。实现该接口的具体方法可以被改写成 Lambda 语句。

定义一个函数接口

```java
@FunctionalInterface
interface Excite {
    String accept(String from);
}
```

以上使用了注解 `@FunctionalInterface`，在 Java 1.8 的初期版本这个注解用于标示一个接口为函数接口，但在现在的版本已经可以省略这个注解了。

#### 使用 Lamda 表达式

Lambda 表达式的基本语法为

```java
(参数列表) -> {执行语句}
```

如果执行语句只有一句的话可以省略包裹其的花括号

例

```java
Excite excite = (word) -> word + "!!";
```

然后我们可以很方便的调用这个接口

```java
excite.accept("Java")
```

如果 Lambda 语句只有一个语句且只有一个参数，且该语句调用的是一个静态方法，则可以使用符号 `::` 进一步缩减代码

```java
Excite hello = (w) -> String.valueOf(w);
```

以上等同于

```java
Excite hello = String::valueOf;
```

如果 Lambda 语句只有一个语句，且该语句为使用类的无参构造方法创建类的实例，则也可以使用符号 `::` 进一步缩减代码

```java
Excite hello = (w) -> new Word();
```

以上等同于

```java
Excite hello = Word::new;
```



#### 多个参数

函数接口也可以接收多个参数，这些参数可以为泛型而不是具体类型，实际上使用泛型的函数接口更为常见

以下定义了一个接收两个参数 `F1` 和 `F2`，返回 `T` 类型的接口

```java
interface Convert<F1, F2, T> {
    T convert(F1 from1, F2 from2);
}
```

使用该接口

```java
Convert<Integer, Integer, String> convert = (x, y) -> {
    int result = x + y;
    return x + " plus " + y + " is " + result;
};
System.out.println(convert.convert(1, 2));  //  1 plus 2 is 3
```

#### 变参

在 Lambda 表达式中也一样可以使用变参，具体例子如下

定义一个含有变参的接口

```java
interface Contact<F, T> {
    T accept(F... from);
}
```

使用该接口

```java
Contact<String, String> contact = (args) -> String.join(",", args);
contact.accept("Java", "Groovy", "Scala", "Kotlin");
```

#### 内置函数接口

通过以上例子我们可以看到要想使用 Lambda 表达式我们必须先定义一个函数接口，这样用法太过麻烦。所以 Java 提供了一些内置的函数接口供我们调用.

##### Predicate

Predicate 接口用于接收一个参数并返回 Boolean 值，主要用于处理逻辑动词。该接口还有一个默认方法 `negate()` 用于进行逻辑取反。（Java 1.8 以前接口不能定义默认行为，相关内容会在接口那一章谈到）

```java
Predicate<String> predicate = (s) -> s.length() > 0;
assert predicate.test("foo");
assert !predicate.negate().test("foo");
```

##### Function

Function 接口接收一个参数并返回单一结果，主要用于进行类型转换等功能。该接口也提供了一个 `andThen()` 方法用于执行链式操作。

```java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);
assert toInteger.apply("123") == 123;
assert backToString.apply("123").equals("123");
```

##### Supplier

Supplier 接口没有参数，但是会返回单一结果，可以用于实现工厂方法。

```java
Supplier<Calculator> calculatorSupplier = Calculator::new;
assert calculatorSupplier.get().add(1, 2) == 3;
```

##### Consumer

Consumer 接口接收一个参数，没有返回值，用于对传入的参数进行某些处理。该接口也提供了 `andThen()` 方法。

```java
Consumer<Person> calculatorConsumer = (p) ->
        System.out.println("The name is " + p.getName());
calculatorConsumer.accept(new Person("Peter"));
```

##### Comparator

Comparator 接口接收两个参数，返回 int 值，用于进行排序操作。该接口提供了 `reversed()` 方法进行反序排列。

```java
Comparator<Person> comparator = (p1, p2) ->
        p1.getAge().compareTo(p2.getAge());
Person john = new Person("John", 20);
Person alice = new Person("Alice", 18);

assert comparator.compare(john, alice) > 0;
assert comparator.reversed().compare(john, alice) < 0;
```

#### 函数接口作为参数

函数接口也可以作为参数来使用

```java
private static int max(int[] arr, Function<int[], Integer> integerFunction) {
    return integerFunction.apply(arr);
}
```

使用该接口

```java
int maxValue = max(new int[]{3, 10, 2, 40}, (s) -> {
    int max = -1;
    for (int n : s) {
        if (max < n) max = n;
    }
    return max;
});
assert maxValue == 40;
```

## Groovy 篇

### 方法

#### 定义方法

完整的 Groovy 方法定义语法为

```
[访问控制符] [static] def 方法名(参数列表)
```

Groovy 也和 Java 一样有成员方法和静态方法之分。

#### Varargs

Groovy 表示变参的方式与 Java 一样 

```groovy
class Calculator {
    def sum(int ... n) {
        print(n.sum())
    }
}
```

调用该方法

```groovy
def calculator = new Calculator()
calculator.sum(1, 2, 3)
```

#### 方法的默认值

Groovy 支持方法默认值，且设置时最后一个或多个连续的参数都必须有默认值

``` groovy
static def say(name, word = "Hello") {
    println("$word $name")
}

say("Peter")
```

如果以上 `say()` 方法替换成以下形式则是非法的，因为最后一个参数没有默认值

```groovy
static def say(name, word = "Hello", bye) {
    println("$word $name")
}
```

#### 返回值

Groovy 中由动态类型的存在，所以可以不声明返回值类型，且方法的最后一个语句的执行结果总被返回（也适用于无返回值的时候），所以也无需 `return` 语句。

```groovy
def add(x, y) {
    x + y
}
```

### 闭包

闭包指的是带有自由变量的代码块。对于 Java 程序员，闭包是一个非常难以理解的概念，这点只能慢慢领会。

#### 创建一个闭包

由于闭包是个代码块，所以最简单的闭包形式如下

```groovy
{ println("foo") }
```

不过由于 Java 的普通代码块也是这样的形式，所以为了避免混淆，通常写成如下形式

```groovy
{ -> println("foo") }
```

综上所述，闭包的语法为

```
{ 参数列表 -> 执行语句 }
```

例

```groovy
{ x, y ->
    println "$x plus $y is ${x + y}"
}
```

Groovy 中定义闭包实际是定义了一个继承自 `Closure` 类的匿名内部类，执行闭包实际是执行该类的实例的方法。

#### 字面量

闭包也可以存储在一个变量中

```groovy
def excite = { word ->
    "$word!!"
}
```

#### 调用闭包

```groovy
excite("Java")
```

或

```groovy
excite.call("Groovy")
```

#### 多参数

闭包也可以拥有多个参数

```groovy
def plus = { int x, int y = 1 ->
    println "$x plus $y is ${x + y}"
}
```

#### it

`it` 是个隐式参数，当闭包只有一个参数时，使用 `it` 可以指代该参数。

```groovy
def greeting = { "Hello, $it!" }
greeting("Peter")
```

#### Varargs

闭包也支持变参

```groovy
def contact = { String... args -> args.join(',') }
println(contact("Java", "Groovy", "Scala", "Kotlin"))
```

#### 闭包作为参数

由于闭包本质是 `Closure` 的子类，所以可以使用 `Closure` 作为参数的类型接收一个闭包

```groovy
static def max(numbers, Closure<Integer> closure) {}
```

进一步简化后

```groovy
static def max(numbers, cls) {
    cls(numbers)
}
```

传入闭包

```groovy
def maxValue = max([3, 10, 2, 1, 40]) {
    def list = it as List<Integer>
    list.max()
}
assert maxValue == 40
```

## Scala 篇

## Kotlin 篇

