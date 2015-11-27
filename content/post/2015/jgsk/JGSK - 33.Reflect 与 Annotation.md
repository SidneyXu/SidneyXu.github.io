---
comments: true
date: 2015-11-26T11:24:40+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-33-reflect-annotation"
tags:
- java
- groovy
- scala
- kotlin
title: "JGSK - 33.Reflect 与 Annotation"
toc: true
topics:
- JGSK
---

## Java

### 注解

注解主要用于标示元数据。Java 中声明一个注解使用符号 `@interface`。

#### 创建一个注解

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Bean {
    String name();
}
```

以上创建了一个名为 `Bean` 的注解。该注解上使用了标示此自定义注解的两个 JVM 的内置注解：`Retention` 和 `Target`。

`Rentention` 表示应该将该注解信息保存在哪里，有 `RUNTIME`，`CLASS`，`SOURCE` 三种。其中 `CLASS` 为默认值，只有标示为 `RUNTIME` 的注解才可以被反射。

`Target` 表示应该将注解放在哪里，有 `TYPE`，`FIELD`，`METHOD`，`PARAMETER` 等几种。即声明为 `TYPE` 时可以将注解放在类或接口上，声明为 `FIELD` 则只能方法属性上。

以上创建的注解中还包含有一个属性 `name`，在使用该注解时必须同时定义此属性。如果使用 `String name() default "";` 替代的话则可以不定义此属性而使用默认值。

#### 使用注解

创建三个注解

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Bean {
    String name();
}

@Retention(RetentionPolicy.CLASS)
@Target(ElementType.FIELD)
public @interface BeanField {
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface BeanMethod {
    String alias() default "";
}
```

以上创建的注解 `Bean` 使用时必须定义 `name` 属性，而 `BeanMethod` 的 `alias` 属性由于有默认值空字符串，所以定义时可以省略 `alias` 属性。

定义一个使用以上注解的类

```java
@Bean(name = "t_person")
class Person {

    public Person() {
    }

    public Person(int age) {
        this.age = age;
    }

    @BeanField
    private int age;

    @BeanMethod(alias = "trueAge")
    public int getAge() {
        return age;
    }

    public void setAge(final int age) {
        this.age = age;
    }

    @BeanMethod(alias = "hello")
    public void sayHello(String message) {
        System.out.println("hello " + message);
    }
}
```

### 反射

所谓的反射即是在运行时获得类的各种元数据（类本身，类中的方法，类中的属性等）的一种手段。实际开发中反射主要用于编写各种工具，我们需要自己编写反射的情况实际非常少。

#### 类的引用

为了对一个类进行反射需要先获得类的信息，即获得类的引用，Java 中需要使用以下方法

```java
Class<?> clazz = Person.class;
```

#### 方法的引用

方法存在于类中，所以获得方法的引用前需要先获得类的引用。

```java
Method setAgeMethod = clazz.getMethod("setAge", int.class);
```

以上使用了 `getMethod()` 方法获得了 `Person` 类中的 `setAge(int)` 方法的引用。`getMethod()` 方法的第一个参数为方法名，之后接着的为表示方法的参数列表的变参。

>与 `getMethod()` 类型还有一个名为 `getDeclaredMethod()` 的方法，两者最显著的区别是前者只能获得公开成员，而后者可以获得私有成员。基本上方法，属性，注解等的引用都有这两种方法。

获得方法的引用后，可以通过 `invoke()` 执行该方法。`invoke()` 的第一个参数为需要执行该方法的对象，之后接着的为传入该方法的参数列表。

```java
Method setAgeMethod = clazz.getMethod("setAge", int.class);
setAgeMethod.invoke(person, 100);
Method getAgeMethod = clazz.getMethod("getAge");
int age = (int) getAgeMethod.invoke(person);
System.out.println("Age is " + age);    //  100
```

#### 属性的引用

属性的引用基本同方法的引用。

```java
Field ageField = clazz.getDeclaredField("age");
```

通过属性的引用来获得属性的值只需要通过 `getXXX()` 之类的方法就可以了，该方法参数为持有该属性的对象。

```java
ageField.setAccessible(true);
System.out.println("Age is " + ageField.getInt(person));
```

以上由于 `age` 在声明时为私有变量，所以需要先使用 `setAccessible()` 才能进行访问。

#### 构造方法的引用

构造方法的引用也和其它引用差不多，直接看例子就可以了。

```java
Constructor<Person> constructor = (Constructor<Person>) clazz.getConstructor(int.class);
Person person1 = constructor.newInstance(18);
System.out.println("Age is " + person1.getAge());
```

#### 注解的引用

```java
Bean bean = clazz.getAnnotation(Bean.class);
String name = bean.name();
System.out.println("Name is " + name);  //  t_person
```

#### 遍历类的成员

实际开发中由于反射主要用于编写工具，所以大部分情况下我们都不知道反射的类的结构，所以通常都需要对类的成员进行遍历并结合以上的所有方法。下面是一个简单的例子。

```java
Field[] fields = clazz.getDeclaredFields();
for (Field field : fields) {
    Annotation[] annotations = field.getDeclaredAnnotations();
    if (annotations == null || annotations.length == 0) continue;
    System.out.println("Find field annotation " + annotations[0].annotationType().getSimpleName());
}

Method[] methods = clazz.getDeclaredMethods();
for (Method method : methods) {
    Annotation[] annotations = method.getDeclaredAnnotations();
    if (annotations == null || annotations.length == 0) continue;
    System.out.println("Find method annotation " + annotations[0].annotationType().getSimpleName());  //  BeanMethod

    BeanMethod beanMethod = (BeanMethod) annotations[0];
    String alias = beanMethod.alias();
    System.out.println("Alias is " + alias);    //  trueAge

    if (method.getName().equals("sayHello")) {
        method.invoke(person, "world");
    }
    System.out.println("====================");
}
```

## Groovy

### 注解

Groovy 用法同 Java。

#### 创建一个注解

```groovy
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Bean {
    String name()
}
```

#### 使用注解

创建三个注解

```groovy
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Bean {
    String name()
}

@Retention(RetentionPolicy.CLASS)
@Target(ElementType.FIELD)
@interface BeanField {
}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface BeanMethod {
    String alias() default ""
}

```

定义一个使用以上注解的类

```groovy
@Bean(name = "t_person")
class Person {

    @BeanField
    def int age

    @BeanMethod(alias = "trueAge")
    def int getAge() {
        age
    }

    def void setAge(int age) {
        this.age = age
    }

    @BeanMethod(alias = "hello")
    def void sayHello(String message) {
        println("hello $message")
    }
}
```

### 反射

Groovy 中的反射与 Java 一样，不过 Groovy 中拥有更强大的元编程，在以后会提起。

#### 类的引用

```groovy
def clazz = Person.class
```

#### 方法的引用

```groovy
def setAgeMethod = clazz.getMethod("setAge", int.class)
```

执行该方法

```groovy
def setAgeMethod = clazz.getMethod("setAge", int.class)
setAgeMethod.invoke(person, 100)
def getAgeMethod = clazz.getMethod("getAge")
int age = (int) getAgeMethod.invoke(person)
println("Age is " + age)    //  100
```

除了使用 `invoke()`，Groovy 还有另一种方式来执行方法，使用时看起来有些像 JavaScript 的 `eval()` 函数。

```groovy
person."${setAgeMethod.name}"(20)
``` 

#### 属性的引用

```groovy
def ageField = clazz.getDeclaredField("age")
```

获得属性的值

``` groovy
ageField.setAccessible(true)
println("Age is " + ageField.getInt(person))
```


#### 构造方法的引用

如果使用构造方法的引用则必须先定义需要的构造方法，但这样会丧失了 Groovy 构造方法的带名参数的功能。

```groovy
def constructor = clazz.getConstructor(int.class)
def person1 = constructor.newInstance(18)
println("Age is " + person1.getAge())
```

#### 注解的引用

```groovy
Bean bean = clazz.getAnnotation(Bean.class)
def name = bean.name()
println("name is " + name)
```

#### 遍历类的成员

```groovy
clazz.declaredFields.findAll {
    it.declaredAnnotations != null && it.declaredAnnotations.size() > 0
}.each {
    println("Find field annotation ${it.annotations[0].annotationType().simpleName}")
}

clazz.declaredMethods.findAll {
    it.declaredAnnotations != null && it.declaredAnnotations.size() > 0
}.each {
    println("Find method annotation ${it.annotations[0].annotationType().simpleName}")

    def alias = (it.annotations[0] as BeanMethod).alias()
    println("Alias is $alias")

    if (it.name == "sayHello") {
        it.invoke(person, "world")
    }
    println("====================")
}
```


## Scala


## Kotlin


## 总结

- 

---

项目源码见 [JGSK/_33_reflect_annotation](https://github.com/SidneyXu/JGSK)
