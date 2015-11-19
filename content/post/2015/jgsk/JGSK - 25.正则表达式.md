---
comments: true
date: 2015-10-04T21:25:26+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-25-regex"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 25.正则表达式
toc: true
topics:
- JGSK
---

## 共通篇

正则表达式功能非常强大，但是写好一个满足各种情况的合格的正则表达式却不是件简单的事。

本章节不会介绍任何正则表达式的语法，而是介绍四种语言中正则表达式最基本的三种用法：匹配，查找和替换的实现方式。

<!--more-->

本章中，在这四种语言中使用正则表达式会碰到以下几个概念:

- *Input* 用于进行正则匹配的字符串
- *Regex* 表示正则表达式的字符串
- *Pattern* 用于编译正则表达式
- *Matcher* 用于处理正则匹配的结果

通常来说只有进行查找操作时才需要手动建立以上几个对象，其它操作时可以直接使用字符串本身提供的简便方法。

## Java 篇

### 创建正则相关的对象

Regex

Regex 只是普通的字符串，不过由于 Java 中 `\` 是特殊符号，所以必须使用转义符。

```java
String regex = "[0-9]+\\W";
```

Pattern

```java
Pattern pattern = Pattern.compile(regex);
```

Matcher

```java
Matcher matcher = pattern.matcher(input);
```

### 使用正则

#### Matching

进行匹配操作时通常只要直接使用字符串的匹配方法就可以了，它会隐式调用 Pattern, Matcher 等类来简化操作。

```java
if ("Hello".matches("[A-Z][a-z]+")) {
    System.out.println("ok");
}
```

#### Searching

#### 基本用法

```java
String input = "99 bottles, 98 bottles";
Pattern pattern = Pattern.compile("[0-9]+\\W");
Matcher matcher = pattern.matcher(input);
while (matcher.find()) {
    System.out.println("find all " + matcher.group());  //  99  98
}
```

以上 `matcher.group()` 表示找到的结果。可以看到用于 `\` 在 Java 中是特殊符号，所以必须写成 `\\`，因此从其它语言的例子中的正则拷贝到 Java 中通常都要做些修改，非常麻烦。

#### Named Group

正则表达中可以使用小括号 `()` 表示分组。在 Java 7 以后支持了命名分组，即可以通过指定的分组名来获得匹配结果，而无需使用分组的索引来获得。分组的基本语法为 `(?<组名>其它操作)`。

```java
//	指定了第一个分组名为 year，第二个为 month
Matcher dateMatcher = Pattern.compile("(?<year>\\d{4})-(?<month>\\d{2})").matcher("2015-10");
if (dateMatcher.matches()) {
	//	通过组名获得结果
    System.out.println(dateMatcher.group("year"));  //  2015
    System.out.println(dateMatcher.group("month")); //  10
    System.out.println(dateMatcher.groupCount());   //  2
    for (int i = 0; i <= dateMatcher.groupCount(); i++) {
    	//	通过索引获得结果
        System.out.println(dateMatcher.group(i));   //  [2015-10, 2015, 10]
    }
}
```

>注意：分组匹配结果的 `0` 索引为全集，从 `1` 开始才是各组的结果。

#### Replacing

替换通常也可以直接使用字符串的方法。

```java
String result = matcher.replaceFirst("xxx");
System.out.println(result);    //  xxxbottles, 98 bottles
```


## Groovy 篇

### 创建正则相关的对象

Regex

Groovy 中使用 `/xxx/` 创建正则表达式，这种使用方式不用像 Java 一样使用转义符就可以使用特殊字符。

```groovy
def pattern = /[0-9]+\W/
```

Pattern

Regex 在 Groovy 中通常可以直接使用。不过为了效率考虑应该使用符号 `~` 进行预编译成 Pattern 对象。

```groovy
def pattern = ~/[0-9]+\W/
```

Matcher

Groovy 使用 `=~` 来创建 Matcher，且在布尔环境中可以直接用于表示是否匹配。

```groovy
def matcher = input =~ pattern
```

### 使用正则

#### Matching

```groovy
if ("Hello" =~ ~/[A-Z][a-z]+/) {
    println("ok")
}
```

#### Searching

#### 基本用法

```groovy
for( matchString in input.findAll(pattern)){
    println("find all $matchString")    //  99  98
}
```

#### Named Group

Groovy 中并不支持命名分组，但是可以使用 Java 7的方式来实现。

除此之外，也可以通过闭包参数来自动转换，使之看起来像使用了命名分组一样。

```groovy
("2015-10" =~ /(\d{4})-(\d{2})/).each { match, year, month ->
    println(match)  //  2015-10
    println(year)   //  2015
    println(month)  //  10
}
```

#### Replacing

替换通常也可以直接使用字符串的方法。

```groovy
def matcher = input =~ pattern
def result = matcher.replaceFirst("xxx")
println(result) //  xxxbottles, 98 bottles
```


## Scala 篇


### 创建正则相关的对象

Regex

Scala 中 Regex 是类，不是普通字符串。在 Scala 中正则通常只需要使用 Regex 对象就可以了。

```scala
val regex = """[0-9]+\W""".r
```

为了使用转义符，通常可以使用原样输出 `"""` 来定义表示正则的字符串。

Pattern 等类也有，但一般无需使用。

### 使用正则

#### Matching

```scala
if ("Hello".matches("[A-Z][a-z]+"))
    println("ok")
```

#### Searching

#### 基本用法

```scala
for (matchString <- pattern.findAllIn(input)) {
    println(s"find all $matchString") //  99  98
}
```

#### Named Group

Scala 中使用 `r()` 来指定分组名。

```scala
"""(\d{4})-(\d{2})""".r("year", "month").findAllIn("2015-10").matchData.foreach { m =>
    println(m.group("year")) //  99
    println(s"group count is ${m.groupCount}") //  2
    for (i <- 0 to m.groupCount)
        println(s"group $i is ${m.group(i)}") //  [2015-10, 2015, 10]
}
```

#### 模式匹配

模式匹配也可以用于正则表达式的匹配

```scala
val pattern2 = "[A-Z][a-z]+".r
"Hello" match {
    case pattern2(c) => println("ok")
    case _ =>
}
```

#### Replacing

```scala
val result = pattern.replaceFirstIn(input, "xxx")
println(result) //  xxxbottles, 98 bottles
```



## Kotlin 篇


### 创建正则相关的对象

Regex

Kotlin 同 Scala 一样只需使用 Regex 对象。

```kotlin
val regex = """[0-9]+\W"""..toRegex()
```

### 使用正则

#### Matching

```kotlin
if ("Hello".matches("[A-Z][a-z]+".toRegex())) {
    println("ok")
}
```

#### Searching

#### 基本用法

```kotlin
for ( matchResult in pattern.findAll(input)) {
    println("find all ${matchResult.value}")    //  99  98
}
```

#### Group

Kotlin 不支持命名分组，只支持使用索引。

```kotlin
"""(\d{4})-(\d{2})""".toRegex().findAll("2015-10").forEach {
    println(it.groups.size
    for (rs in it.groups) {
        println(rs?.value)  //  [2015-10, 2015, 10]
    }
}
```

#### Replacing

```kotlin
val result = pattern.replaceFirst(input, "xxx")
println(result)
```


## 总结

- Java 和 Scala 都支持带名分组。
- Scala 和 Kotlin 可以只使用 Regex 对象完成正则匹配。
- Scala 的模式匹配也可以用于正则表达式。


---

项目源码见 [JGSK/_25_package](https://github.com/SidneyXu/JGSK)

