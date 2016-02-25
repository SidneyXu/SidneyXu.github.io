---
comments: true
date: 2015-11-22T22:49:33+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-31-Test"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 31.Test
toc: true
topics:
- JGSK
---

## 共通篇

### 测试过程分类

测试过程通常有 N 种分类，一般常说的有那么几种： UT，IT(CT)，ST，MT。

UT 单元测试属于白盒测试，是测试中的最小单元，一般用于测试单独的方法，检测方法的正确性。

<!--more-->

IT 集成测试（又可称为 CT 结合测试），属于灰盒测试，在单元测试之后进行，一般用于测试各个接口之间的数据传递功能，由数个单元测试的对象组成。

ST 系统测试，属于黑盒测试，在集成测试之后进行，用于测试系统与原定于的需求的契合度。

MT 随机测试，属于黑盒测试，测试要求随机，可以无需知道任何业务逻辑，主要用于测试系统的稳定性。

### 测试相关技术

**TDD**

TDD 为测试驱动开发。步骤一般是先按照预期的调用方式编写调用测试用例，然后做最小修改使测试用例可以跑通，然后继续写下一个测试用例，所有测试用例跑通后一个可用的类便编写完成。

TDD 的最大优点就是编写的代码是可测试的，容易调用的，解决了空想造成的代码耦合度高，不可测试，很难调用的问题。有不少人提倡所有开发都应该使用 TDD 技术，但是 TDD 本身会加大工作量，有时会为了不必要的解耦增加系统的复杂性。

**BDD**

BDD 为行为驱动开发。其最大的特色是编写测试用例之前先使用自然语言来编写该段代码的目的，本质上是对原来的测试驱动开发做了扩展。由于使用了自然语言来进行描述，因此非程序员也能进行一定程度的阅读，而不用等程序员来进行翻译。

## Java

### 概述

Java 中 Unit Test 主要有这么三个概念：Test Runner，Test Suit，Test Case。

- Test Runner 主要用于运行测试用例。
- Test Suit 用于收集 Test Case，是 Test Case 的集合。
- Test Case 即测试用例，默认的 Test Case 访问权限为 `public` 且方法名开头为 `test`。

### 实现 UnitTest

第一种方式是继承 `TestCase` ，该类有两个特殊方法 `setUp()` 和 `tearDown()`，前者在每个测试用例执行前执行，后者在每个测试用例执行完执行。所有以 `test` 开头的方法都会被认为是测试用例。

```java
public class Test01 extends TestCase {

    private Calculator calculator;

    @Override
    public void setUp() throws Exception {
        super.setUp();
        calculator = new Calculator();
    }

    @Override
    public void tearDown() throws Exception {
        super.tearDown();
        System.out.println("End test...");
    }

    public void testSum() {
        assert 3 == calculator.sum(1, 2);
        assert 3 != calculator.sum(2, 5);
    }

    public void testMock() {
        Calculator calculator = Mockito.mock(Calculator.class);
        Mockito.when(calculator.sum(1, 2)).thenReturn(10);
        assert 10 == calculator.sum(1, 2);
        assert 0 == calculator.sum(1, 20);
    }

    public void testStory() {
        Animal elephant = new Animal("Elephant");
        Animal giraffa = new Animal("Giraffa");
        Refrigerator refrigerator = new Refrigerator();

        refrigerator.putInto(giraffa);
        assert !refrigerator.isEmpty();

        refrigerator.takeOut();
        refrigerator.putInto(elephant);
        assert !refrigerator.isEmpty();

        Animal animal = refrigerator.takeOut();
        assert refrigerator.isEmpty();
        assertEquals(animal, elephant);
    }

    private class Calculator {
        public int sum(int x, int y) {
            return x + y;
        }
    }

    private class Refrigerator {
        private Animal animal;

        public boolean putInto(Animal animal) {
            if (isEmpty()) {
                this.animal = animal;
                return true;
            }
            return false;
        }

        public Animal takeOut() {
            if (isEmpty()) return null;
            Animal temp = animal;
            animal = null;
            return temp;
        }

        public boolean isEmpty() {
            return animal == null;
        }
    }

    private class Animal {
        public String type;

        public Animal(String type) {
            this.type = type;
        }
    }
}
```

第二种方式是使用注解，适合那些无法继承 TestCase 的类。标为 `@Test` 的方法会被收集为测试用例，标为 `@Before` 的方法会在每个测试用例执行前执行，标为 `@After` 的方法会在每个测试用例后执行。

```java
public class Test02 {

    private Calculator calculator;

    @Before
    public void setUp() {
        calculator = new Calculator();
    }

    @After
    public void tearDown() {
        System.out.println("End test...");
    }

    @Test
    public void testSum() {
        assert 3 == calculator.sum(1, 2);
        assert 3 != calculator.sum(2, 5);
    }

    @Test
    public void testMock() {
        Calculator calculator = Mockito.mock(Calculator.class);
        Mockito.when(calculator.sum(1, 2)).thenReturn(10);
        assert 10 == calculator.sum(1, 2);
        assert 0 == calculator.sum(1, 20);
    }

    private class Calculator {
        public int sum(int x, int y) {
            return x + y;
        }
    }
}
```

## Groovy

### 实现 UnitTest

继承 `GroovyTestCase`

```groovy
class Test01 extends GroovyTestCase {

    private Calculator calculator

    @Override
    protected void setUp() throws Exception {
        super.setUp()
        calculator = new Calculator()
    }

    @Override
    protected void tearDown() throws Exception {
        super.tearDown()
        println("End test...")
    }

    def testSum() {
        assert 3 == calculator.sum(1, 2)
        assert 3 != calculator.sum(2, 5)
    }

    def testMock() {
        Calculator calculator = Mockito.mock(Calculator.class)
        Mockito.when(calculator.sum(1, 2)).thenReturn(10)
        assert 10 == calculator.sum(1, 2)
        assert 0 == calculator.sum(1, 20)
    }

    def testStory() {
        Animal elephant = new Animal(type: "Elephant")
        Animal giraffa = new Animal(type: "Giraffa")
        Refrigerator refrigerator = new Refrigerator()

        refrigerator.putInto(giraffa)
        assert !refrigerator.isEmpty()

        refrigerator.takeOut()
        refrigerator.putInto(elephant)
        assert !refrigerator.isEmpty()

        Animal animal = refrigerator.takeOut()
        assert refrigerator.isEmpty()
        assertEquals(animal, elephant)
    }

    class Calculator {
        def sum(x, y) {
            x + y
        }
    }

    class Refrigerator {
        private Animal animal

        def putInto(Animal animal) {
            if (isEmpty()) {
                this.animal = animal
                true
            }
            false
        }

        def Animal takeOut() {
            if (isEmpty()) return null
            def temp = animal
            animal = null
            temp
        }

        def isEmpty() {
            animal == null
        }
    }

    class Animal {
        public String type
    }
}
```

Groovy 也能使用注解实现测试用例，但是使用注解时必须使用静态声明，没有继承方便。


## Scala

### 实现 UnitTest

```scala
class Test01 extends TestCase {
  private var calculator: Calculator = null

  override def setUp() {
    calculator = new Calculator()
  }

  override def tearDown() {
    println("End test...")
  }

  def testSum() {
    assert(3 == calculator.sum(1, 2))
    assert(3 != calculator.sum(2, 5))
  }

  def testMock() {
    val calculator: Calculator = Mockito.mock(classOf[Calculator])
    Mockito.when(calculator.sum(1, 2)).thenReturn(10)
    assert(10 == calculator.sum(1, 2))
    assert(0 == calculator.sum(1, 20))
  }

  def testStory() {
    val elephant: Animal = new Animal("Elephant")
    val giraffa: Animal = new Animal("Giraffa")
    val refrigerator: Refrigerator = new Refrigerator
    refrigerator.putInto(giraffa)
    assert(!refrigerator.isEmpty)
    refrigerator.takeOut
    refrigerator.putInto(elephant)
    assert(!refrigerator.isEmpty)
    val animal: Animal = refrigerator.takeOut
    assert(refrigerator.isEmpty)
    assert(animal == elephant)
  }
}

class Calculator {
  def sum(x: Int, y: Int): Int = x + y
}

class Refrigerator {
  private var animal: Animal = null

  def putInto(animal: Animal): Boolean = {
    if (isEmpty) {
      this.animal = animal
      true
    } else {
      false
    }
  }

  def takeOut: Animal = {
    if (isEmpty) {
      null
    } else {
      val temp = animal
      animal = null
      temp
    }
  }

  def isEmpty: Boolean = {
    animal == null
  }
}

class Animal(val `type`: String)
```

### 实现 ScalaTest

Scala Test 是 Scala 的一个测试框架，比起直接使用 JUnit 能够写出更优美的代码。

```scala
@RunWith(classOf[JUnitRunner])
class ScalaTest01 extends FunSuite with BeforeAndAfter {

  private var calculator: Calculator = null

  before {
    calculator = new Calculator
  }

  after {
    println("End test...")
  }

  test("Sum") {
    assert(3 == calculator.sum(1, 2))
    assert(3 != calculator.sum(2, 5))
  }

  test("Story") {
    val elephant: Animal = new Animal("Elephant")
    val giraffa: Animal = new Animal("Giraffa")
    val refrigerator: Refrigerator = new Refrigerator
    refrigerator.putInto(giraffa)
    assert(!refrigerator.isEmpty)
    refrigerator.takeOut
    refrigerator.putInto(elephant)
    assert(!refrigerator.isEmpty)
    val animal: Animal = refrigerator.takeOut
    assert(refrigerator.isEmpty)
    assert(animal == elephant)
  }
}
```

### 实现 Spec2

Spec2 是另一个 Scala 的测试框架，使用它可以简单的完成 BDD 的测试用例的编写。

```scala
class SpecTest extends Specification with BeforeAfterEach with Mockito {

    private var calculator: Calculator = null
    override protected def after: Any = {
        println("End test...")
    }
    override protected def before: Any = {
        calculator = new Calculator
    }

    "Arithmetic" should {
        "sum" in {
            "two numbers" in {
                calculator.sum(1, 2) mustEqual 3
                calculator.sum(2, 5) mustNotEqual 3
            }
        }
    }

    "TestMock" should {
        "mock sum method" in {
            "two numbers" in {
                val calculator = mock[Calculator]
                calculator.sum(1, 2) returns 10
                calculator.sum(1, 2) mustEqual 10
                calculator.sum(1, 20) mustEqual 0
            }
        }
    }

    "Story" should {
        val elephant: Animal = new Animal("Elephant")
        val giraffa: Animal = new Animal("Giraffa")
        val refrigerator: Refrigerator = new Refrigerator

        """
           1. put giraffa
           2. takeout giraffa
           3. put elephant
           4. takeout elephant
           the refrigerator should be empty""" in {
            refrigerator.putInto(giraffa)
            refrigerator.isEmpty mustEqual false

            refrigerator.takeOut
            refrigerator.isEmpty mustEqual true

            refrigerator.putInto(elephant)
            val animal: Animal = refrigerator.takeOut
            refrigerator.isEmpty mustEqual true
            animal mustEqual elephant
        }

    }
}
```

## Kotlin

### 实现 UnitTest

第一种方式，继承 TestCase

```kotlin
class Test01 : TestCase() {

    private var calculator: Calculator? = null

    override fun setUp() {
        super.setUp()
        calculator = Calculator()
    }

    override fun tearDown() {
        super.tearDown()
        println("End test...")
    }

    fun testSum() {
        assert(3 == calculator?.sum(1, 2))
        assert(3 != calculator?.sum(2, 5))
    }

    fun testMock() {
        val calculator = Mockito.mock<Calculator>(Calculator::class.java)
        Mockito.`when`<Int>(calculator.sum(1, 2)).thenReturn(10)
        assert(10 == calculator.sum(1, 2))
        assert(0 == calculator.sum(1, 20))
    }

    fun testStory() {
        val elephant = Animal("Elephant")
        val giraffa = Animal("Giraffa")
        val refrigerator: Refrigerator = Refrigerator()

        refrigerator.putInto(giraffa)
        assert(!refrigerator.isEmpty())

        refrigerator.takeOut()
        refrigerator.putInto(elephant)
        assert(!refrigerator.isEmpty())

        val animal = refrigerator.takeOut()
        assert(refrigerator.isEmpty())
        assertEquals(animal, elephant)
    }

}

//  Declared open when using mock
open class Calculator {
    open fun sum(x: Int, y: Int): Int {
        return x + y
    }
}

class Refrigerator {
    private var animal: Animal? = null

    fun putInto(animal: Animal): Boolean {
        if (isEmpty()) {
            this.animal = animal
            return true
        }
        return false
    }

    fun takeOut(): Animal? {
        if (isEmpty()) return null
        val temp = animal
        animal = null
        return temp
    }

    fun isEmpty(): Boolean {
        return animal == null
    }
}

class Animal(type: String)
```

第二种方式，使用注解

```kotlin
class Test02 {

    private var calculator: Calculator? = null

    @Before
    fun setUp() {
        calculator = Calculator()
    }

    @After
    fun tearDown() {
        println("End test...")
    }

    @Test
    fun testSum() {
        assert(3 == calculator?.sum(1, 2))
        assert(3 != calculator?.sum(2, 5))
    }

    @Test
    fun testMock() {
        val calculator = Mockito.mock<Calculator>(Calculator::class.java)
        Mockito.`when`<Int>(calculator.sum(1, 2)).thenReturn(10)
        assert(10 == calculator.sum(1, 2))
        assert(0 == calculator.sum(1, 20))
    }

    @Test
    fun testStory() {
        val elephant = Animal("Elephant")
        val giraffa = Animal("Giraffa")
        val refrigerator: Refrigerator = Refrigerator()

        refrigerator.putInto(giraffa)
        assert(!refrigerator.isEmpty())

        refrigerator.takeOut()
        refrigerator.putInto(elephant)
        assert(!refrigerator.isEmpty())

        val animal = refrigerator.takeOut()
        assert(refrigerator.isEmpty())
        assertEquals(animal, elephant)
    }

}
```

### 其它测试框架

Kotlin 还有一种测试框架名为 Spek，同 Scala 的 Specs2 一样，也可以用于进行 BDD 测试，不过目前其还只是 `0.1.x` 版本，还没有达到可以放心用在生成环境的时候，这里就不介绍了，有兴趣可以访问 [官网](http://jetbrains.github.io/spek/index.html) 了解详情。


## 总结

- JUnit 是所有测试的核心框架。
- 一般编写测试用例可以使用继承和注解两种方式，继承的代码量更少，注解更加灵活。
- Scala 除了可以直接使用 JUnit，也可以使用 scalaTest 和 Specs2 进行测试。


---

项目源码见 [JGSK/_31_test](https://github.com/SidneyXu/JGSK)
