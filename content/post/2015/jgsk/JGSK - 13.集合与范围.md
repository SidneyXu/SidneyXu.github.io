---
comments: true
date: 2015-09-18T06:41:53+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-13-collection-and-range"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 13.数组，集合与范围
toc: true
topics:
- JGSK
---

## 共通篇

数组在程序中用于表示一组特定的值，通常来说数组的大小是预先指定的，数组的元素类型也是统一的，所以访问数组时可以通过偏移量快速访问任意一个元素。

集合类似数组，很多时候集合也是通过数组实现的，但是集合的长度是可变的，存储的数据类型也可以不一样（尽管一般都是存储同类型的数据）。集合通常有两种：List 和 Set，一般来说，前者有序且元素可以重复，后者无序但元素不能重复。
<!--more-->

## Java 篇

### 数组

定义一个数组的语法如下

```
类型[] 变量名 = new 类型[长度];
```

或者在定义时指定具体的元素

```
类型[] 变量名 = {元素..};
```

例

```java
int[] arr = new int[3];
int[] arr2 = {1, 2, 3};
```

由于数组的长度是固定的，所以无法增加元素也无法删除元素，只能对某一位置的元素进行修改

```java
arr[0] = 10;
```

读取某一位置的元素

```java
System.out.println(arr[0]);
```

获得数组长度

```java
System.out.println(arr.length);
```

遍历数组

```java
for (int a : arr2) {
    System.out.println("Traverse " + a);
}
```

打印数组

默认 `toString()` 只会打印出数组的地址，要想打印数组的内容需要使用工具类 `Arrays`。

```java
System.out.println(Arrays.toString(arr2));
```

### 集合

#### List

List 表示列表，是集合中最常用的类型，Java 中的 List 默认是可变的，其本身是一个接口，最常用的是其子类 `ArrayList` 和 `LinkedList`，前者用于随机访问，后者用于实现队列。

##### 定义一个 List

```java
List<String> list = new ArrayList<String>();
```

默认创建的列表是空列表，里面没有任何元素。这种两边都要写明列表中元素类型的语法一直以来都被认为是非常愚蠢的，所以 Java 7 以后提供了被称作 Diamond 的语法，可以省略等式右边的类型声明。

```java
List<String> list = new ArrayList<>();
```

实际上这样也没有进步多少，Guava 包提供的语法还是更加简单 `List<String> list = Lists.newArrayList();`。

需要注意的是在列表的工具类 `Collections` 中也有一个可以提供空列表的方法，但那个返回的是继承自 `AbstractList` 的 `EmptyList` 的实例，该实例不支持任何操作，与 new 出来的空列表并不一样。

```java
List emptyList = Collections.emptyList();
//  试图调用以下方法会报 UnsupportedOperationException
//        emptyList.add(1);
```

##### 修改List

添加元素

```java
list.add("Groovy");
```

也可以使用 `Collections` 的工具方法一次性添加多个元素

```java
Collections.addAll(list, "Groovy", "Java", "Scala");
```

修改元素

```java
list.set(0, "Ruby");
```

这里需要注意的是修改时的第一个参数为索引，索引不能超过该列表的长度，否则会报 `IndexOutOfBoundsException`。

删除元素

```java
//	删除指定位置
list.remove(1);

//	删除自定元素
list.remove("Ruby");
```

##### 访问元素

```java
list.get(1);
```

索引只能是大于等于小于列表长度的整数，否则会报`ArrayIndexOutOfBoundsException`。

##### 通过元素创建List

List 有两种方式可以在创建时就指定其存储的具体元素。

第一种是通过工具类 `Arrays`

```java
List<String> list = Arrays.asList("Groovy", "Java", "Scala");
```

这种方式创建的其实是一个定长的列表，不支持任何会影响其长度的方法，比如说添加或者删除项目均为报 `UnsupportedOperationException`。

第二种是通过初始化块，这种方式创建的是普通的列表，但是不支持 Diamond 语法。

```java
List<String> list = new ArrayList<String>() {{
            add("Groovy");
            add("Java");
            add("Scala");
        }};
```

#### 不可变List

在将列表作为返回值返回时，通常我们不希望列表内容被随意修改，这时可以返回一个不可变列表来禁止任何修改行为。

```java
List<String> immuatbleList = Collections.unmodifiableList(list);
```

以上方法实际是返回了一个 `UnmodifiableCollection` 的实例，该实例使用委托方式调用传入的 `list` 对象，一旦发现执行了任何修改操作就立即抛出异常。

#### 其它操作

获得 List 长度

```java
System.out.println(list.size());
```

注意，列表使用 `size()` ，数组使用 `length`，这种设计一直都是 Java 程序员的槽点之一。

遍历 List

```java
for (String lang : list) {
    System.out.println("Traverse " + lang);
}
```


#### Set

Set 最常用的是其子类 `HashSet`，本质上其实是一个 key-value 都一致的 `HashMap`。由于用法类似，本系列就不详细举例了。

### Range

Java 不支持 Range


## Groovy 篇

### 数组


Groovy 定义数组类似 Java，但是指定具体元素时 Groovy 使用 `[]`而不是 `{}`

```groovy
//	基本语法
def arr = new int[3]

//	定义时指定内容
def arr2 = [1, 2, 3] as int[]
```

注意第二个例子后面的 `as int[]`，因为在 Groovy 中 `[]` 这种语法默认产生的其实时 `List` 对象，所以需要通过 `as` 语法将其转换为数组对象。

修改元素

```groovy
arr[0] = 10
```

读取元素

```groovy
println(arr[0])
```

获得数组长度

```groovy
println(arr.length)
```

遍历数组

```groovy
for (int a : arr2) {
    println("Traverse " + a);
}
```



### 集合

#### List

Groovy 中的 List 概念与 Java 完全一致。

##### 定义一个 List

空列表

```groovy
def emptyList = []
```

指定元素的列表

```groovy
def list = ["Groovy", "Java", "Scala"]
```

以上方式默认创建的是 `ArrayList`

##### 修改List

添加元素

```groovy
list.add("Rust")
list << "Kotlin"
```

以上第二种用法看起来语法更加简单直观

修改元素

```groovy
list[10] = "Ruby"
list.putAt(9, "Python")
```

与 Java 不同的是，Groovy 中如果指定的索引超过了列表的长度，列表会被自动扩容，所以以上做法在 Java 中是非法的，但是在 Groovy 中是允许的。


删除元素

与 Java 相同

```groovy
//	删除指定位置
list.remove(1)

//	删除自定元素
list.remove("Ruby")
```

##### 访问元素

```groovy
list.get(2)
list[2]
```

以上两种方式都可以，其中第二种可以让数组和列表的访问拥有统一的方式

同时 Groovy 还支持使用负数从后往前建立索引，或者使用 `Range` 获得一个子列表

```groovy
list[-1]  
list[-1..-3] 
```

##### 建立新列表

Groovy 可以使用 `+` 和 `-` 从现有列表的元素创建新列表，这些方式不会修改原有列表，需要特别注意

```groovy
def list = ["Groovy", "Java", "Scala", "Kotlin"]
def newList = list - ["Ruby", "Rust", "Kotlin"] + "Swift"
```

以上操作返回一个包含 `Groovy, Java, Scala, Swift` 的新列表。

#### 不可变List

需要使用 Java 的方法

#### 展开操作符

Groovy 中可以使用展开操作符 `*.` 来操作列表中的每一个元素。

```groovy
def numbers = [1, 2, 3, 4, 3, 4]
def numbers2 = numbers*.plus(10)
println(numbers)            //[1, 2, 3, 4, 3, 4]
println(numbers2)           //[11, 12, 13, 14, 13, 14]
```

#### 其它操作

获得 List 长度

```groovy
println(list.size())
```

遍历 List

```groovy
for (lang in list) {
    println("Traverse " + lang);
}
```



#### Set

同 Java

### Range

上一章节已经提到过了，Range 可以表示一个连续范围内的值，可以使 `for` 循环变得更加简单。

定义一个 Range

```groovy
def rng1 = 1..3
println(rng1)   //  1, 2, 3]
```

Range 不仅可以用在数字上，也可以用在字符上

```groovy
def rng4 = 'a'..'c'
println(rng4)   //  [a, b, c]
```

左闭右开

```groovy
def rng2 = 1..<3
println(rng2)   //  [1, 2]
```

指定步长

```groovy
def rng3 = (1..5).step(2)
println(rng3)   //  [1, 3, 5]
```


## Scala 篇


### 数组

#### Array

Array 在 Scala 中属于传统的定长数组。Scala 定义数组的方式类似 Java 中的列表，但是指定具体元素时即不是Groovy 的 `[]` 也不是 Java 的`{}`，而是 `()`。

```scala
//	基本语法
val arr = new Array[Int](3)

//	定义时指定内容
val arr2 = Array(1, 2, 3)
```

修改元素

```scala
arr(0) = 10
```

读取元素

```scala
println(arr(0))
```

获得数组长度

```scala
println(arr2.length)
```

遍历数组

```scala
for (a <- arr2) {
  println(s"Traverse $a")
}
```

打印数组

默认 `toString()` 只会打印出数组的地址，要想打印数组的内容需要使用 `mkString()`。

```scala
println(arr2.mkString(","))
```


#### ArrayBuffer

ArrayBuffer 在 Scala 中属于变长数组，相比较数组而言其最大的缺点就是删除元素时的效率较低，使用时相当于 Java 的 ArrayList。

定义 ArrayBuffer

```scala
val abuffer = ArrayBuffer[Int]()
```

修改元素

```scala
//	添加单个或多个元素
abuffer += 10

//	添加单个或多个 Array 或 ArrayBuffer
abuffer ++= arr2

//	删除单个或多个元素
abuffer -= 3
```

读取元素

```scala
println(abuffer(0))
```

#### Array 和 ArrayBuffer 的转换

Array -> ArrayBuffer

```scala
val buffer = arr.toBuffer
```

ArrayBuffer -> Array

```scala
val arr4 = abuffer.toArray
```


### 集合

#### List

Scala 列表在语言层面就分为不可变列表和可变列表。不可变列表无法改变列表的内容，是 Scala 默认的列表类型。

##### 定义一个不可变列表

空列表

```scala
val empty = List()
println(empty == Nil)	//	true
```

Scala 中空列表等于 `Nil`。且由于列表不可变，所以该空列表也无法进行任何写操作

指定元素的列表

```scala
val list = List("Groovy", "Java", "Scala")
```

#### List 构造器

Scala 的列表结构与其它语言都不一样。它分为头部和尾部，头部就是列表的第一个元素，尾部也是一个列表，它包含列表的其余部分。

```scala
val list = List("Groovy", "Java", "Scala")
println(list.head) // Groovy
println(list.tail) // List(Java, Scala)
println(list.tail.head) //  Java
```

由于是不可变列表，所以即无法添加修改元素，也无法删除元素，但是可以通过符号 `::` 结合当前列表返回新列表。

`::` 是中缀操作符，左边的操作数为组成新列表的元素，右操作数为当前列表，该操作符具有右结合性，即 `A :: B :: C` 会被翻译成 `A :: (B :: C)`

```scala
val list = List("Groovy", "Java", "Scala")
val newList = "Ruby" :: list
//	val newList2 = list :: "Ruby"
```

以上第一个操作会返回包含 `Ruby, Groovy, Java, Scala` 的新列表。第二个操作则是非法的，因为右操作数 `Ruby` 不是列表。

掌握了右结合性的特性，我们就可以写出如下代码

```scala
val days = "Sunday" :: "Monday" :: "Tuesday" :: "Wednesday" :: "Thursday" :: "Friday" :: "Saturday" :: Nil
```

也可以通过操作符 `:::` 结合两个列表的内容产生新列表

```scala
val all = list ::: days
```

##### 访问元素

```scala
println(list(2)) // Scala
```
#### 可变List

可变 List 位于 `scala.collection.mutable` 包下，本质上是 `LinkedList`。

创建一个可变 List

```scala
var mutableList = new mutable.MutableList[Int]
```

可变 List 可以使用符号 `+=` 添加新元素

```
//	添加单个元素
mutableList += 1

//	添加多个元素
mutableList +=(2, 3, 5)
```

访问时与不可变 List 相同

```scala
println(mutableList(1))
```

尽管称作可变 List，但是其并不支持直接删除任意元素

#### ListBuffer

ListBuffer 是另一种可变 List，其本质实际是由 `Nil` 和 `::` 结合不可变 List 来实现的。

创建一个可变 ListBuffer

```scala
var listBuffer = new ListBuffer[Int]
```

可以使用符号 `+=` 添加新元素或者符号 `-=` 删除元素

```
//	添加单个元素
listBuffer += 1

//	添加多个元素
listBuffer +=(2, 3, 5)

//	删除元素
listBuffer -= 2
```

访问时与不可变 List 相同

```scala
println(listBuffer(1))
```

##### List 和 ListBuffer 转换

List -> ListBuffer

```scala
list.toBuffer
```

ListBuffer -> List

```scala
listBuffer.toList
```

#### 其它操作

获得 List 长度

```scala
println(list.length)
```

注意，Scala 中列表和数组都使用了 `length`，解决了 Java 中那个愚蠢的问题。

遍历 List

```scala
for (lang <- list) {
  println(s"Traverse $lang")
}
```

#### Set

Set 使用方式类似 List，这里就不细说了。

### Range


定义一个 Range

```scala
val rng1 = 1 to 3
println(rng1) //  Range(1, 2, 3)
```

Range 不仅可以用在数字上，也可以用在字符上

```scala
val rng4 = 'a' to 'c'
println(rng4) //  NumericRange(a, b, c)
```

左闭右开

```scala
val rng2 = 1 until 3
println(rng2) //  Range(1, 2)
```

指定步长

```scala
val rng3 = 1 to 5 by 2
println(rng3) //  Range(1, 3, 5)
```

除了以上方法，还可以直接通过构造器建立对象，需要注意的是通过构造器建立的范围是左闭右开的

```scala
val rng5 = Range(1, 3)
println(rng5) //  Range(1, 2)

val rng6 = Range(1, 5, 2)
println(rng6) //  Range(1, 3)
```


## Kotlin 篇


### 数组

```kotlin
//	基本语法
val arr = arrayOfNulls<Int>(3)

//	定义时指定内容
val arr2 = arrayOf(1, 2, 3)
```

修改元素

```kotlin
arr[0] = 10
```

读取元素

```kotlin
println(arr[0])
```

获得数组长度

```kotlin
println(arr.size
```

遍历数组

```kotlin
for (a in arr2) {
    println("Traverse $a")
}
```

### 集合

#### List

Kotlin 同 Scala 一样 列表在语言层面就分为不可变列表和可变列表。不可变列表无法改变列表的内容，是 Kotlin 默认的列表类型。

##### 定义一个不可变列表

空列表

```kotlin
val empty = emptyList<Int>()
```

Kotlin 中由于列表不可变，所以该空列表也无法进行任何写操作

指定元素的列表

```kotlin
val list = listOf("Groovy", "Java", "Scala")
```

##### 访问元素

```kotlin
println(list(2)) // Scala
```

#### 可变List

同 Scala 一样，Kotlin 的可变 List 就是 `LinkedList`。

创建一个可变 List

```kotlin
var mutableList = linkedListOf<String>()
```

可变 List 可以使用方法 `add()` 添加新元素，使用方法 `remove()` 删除元素

```kotlin
mList.add("Ruby")

mList.remove("Java")
```

访问时与不可变 List 相同

```kotlin
println(mutableList(1))
```

尽管称作可变 List，但是由于其本身是 `LinkedList`，所以并不支持删除任一索引的元素

#### 其它操作

获得 kotlin 长度

```java
println(list.size)
```

注意，Kotlin 和 Scala 一样也统一了用法，但是 Kotlin 使用的是 `size()`。

遍历 List

```kotlin
for (lang in list) {
    println("Traverse $lang")
}
```

#### Set

Set 使用方式类似 List，这里就不细说了。

### Range


定义一个 Range

```kotlin
val rng1 = 1..3
println(rng1)   //  1..3
```

Range 不仅可以用在数字上，也可以用在字符上

```kotlin
val rng4 = 'a'..'c'
println(rng4)   //  a..c
```

指定步长

```kotlin
val rng3 = (1..5).step(2)
println(rng3)   //  1..5 step 2
```


## 总结

- 除了 Java，其它语言都支持 Range 类型
- Scala 与 Kotlin 默认集合为不可变形式
- Scala 有可变数组和不可变数组两种数组
- Scala 有 Mutable List, Immutable List 和 ListBuffer 三种列表
- Java 和 Groovy 访问数组长度和列表长度分为为 `length` 和 `size()`，Scala 访问数组和列表长度都使用 `length`，Kotlin 都使用 `size()` ╮(╯_╰)╭


---

项目源码见 [JGSK/_13_collection](https://github.com/SidneyXu/JGSK)

