---
comments: true
date: 2015-09-25T11:56:51+08:00
description: ""
draft: false
keywords:
- Android
- test
- Mockito
- PowerMock
- Travis
- Wercker
- Assertj
- Robolectric
- Espresso
slug: ""
tags:
- android
- test
title: Android测试环境选型
toc: true
topics:
-  Test
---

## Android 测试工具

### 分类

Android测试按测试范围通常可以分为两种：Logic Test和 UI Test，前者用于测试各种逻辑是否正常执行，很多情况下无需调用Android的api，后者用于测试UI控件的动作响应是否如预期。

按照测试手段也可以分为两种：Local Test 和 Instrument Test，前者指的是使用JVM直接进行本地测试，通常来说效率要高出不少，后者又称为Connected Test，需要连接模拟器或者才能进行测试。Local Test 的测试代码通常放在 `src/test` 目录，且依赖使用 `testCompile`声明，Instrument Test 的测试代码通常放在 `src/androidTes`目录，且依赖使用 `androidTestCompile` 声明。

<!--more-->

### Logic Test

#### Android Testing Framework

使用方法

修改`build.gradle `

``` gradle
defaultConfig {
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}

dependencies {
    androidTestCompile 'com.android.support.test:runner:0.3'
    androidTestCompile 'com.android.support.test:rules:0.3'
}
```

Sample

``` java
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

@RunWith(AndroidJUnit4.class)
public class JUnit4Test {

    private Context mContext;

    @Before
    public void setUp() throws Exception {
        mContext = InstrumentationRegistry.getInstrumentation().getTargetContext();
    }

    @Test
    public void add() {
        assertThat(Calc.add(3, 5), is(8));
    }

    @Test
    public void context() {
        assertThat(mContext.getString(R.string.app_name), is("example-android-lib"));
    }

}
```

运行测试

``` 
./gradlew connectedAndroidTest
```

测试结果位于 `build/outputs/reports/androidTests/connected/index.html`



#### Robolectric

Robolectric 运行于 JVM 上，所以效率比起需要连接到模拟器的测试方式要快不少。

使用方法

编辑 `build.gradle`

``` gradle
dependencies {
    testCompile "org.robolectric:robolectric:3.0"
}
```

Sample

``` java
@RunWith(RobolectricTestRunner.class)
@Config(sdk = 21, manifest = "./src/main/AndroidManifest.xml",
        constants = BuildConfig.class)
public class RobolectricTest {

    @Before
    public void setup() {
    }

    @After
    public void teardown() {
    }

    @Test
    public void sample() {
        Context context = RuntimeEnvironment.application.getApplicationContext();
        assertThat(
                context.getString(R.string.app_name), is("example-android-lib"));
    }
}

```

运行测试

``` 
./gradlew clean test
```

测试结果位于 `build/outputs/reports/tests/debug/index.html`



### UI Test

#### ActivityInstrumentationTestCase2（Android Instrumentation）

使用方法

修改`build.gradle `

``` gradle
defaultConfig {
    testInstrumentationRunner "android.test.InstrumentationTestRunner"
}
```

Sample

``` java
public class ActivityInstrumentationTestCaseTest extends ActivityInstrumentationTestCase2<CalculatorActivity> {

    public ActivityInstrumentationTestCaseTest() {
        super(CalculatorActivity.class);
    }

    public void testAdd() {
         EditText num1EditText = (EditText) getActivity().findViewById(R.id.number1);
        TouchUtils.clickView(this, num1EditText);
        sendKeys(KeyEvent.KEYCODE_1);

        EditText num2EditText = (EditText) getActivity().findViewById(R.id.number2);
        TouchUtils.clickView(this, num2EditText);
        sendKeys(KeyEvent.KEYCODE_1);
        sendKeys(KeyEvent.KEYCODE_0);

        //  Monitor ResultActivity
        Instrumentation.ActivityMonitor monitor
                = new Instrumentation.ActivityMonitor(ResultActivity.class.getCanonicalName(), null, false);
        getInstrumentation().addMonitor(monitor);

        Button calcButton = (Button) getActivity().findViewById(R.id.calc);
        TouchUtils.clickView(this, calcButton);

        Activity resultActivity = getInstrumentation().waitForMonitorWithTimeout(monitor, 5000);

        assertEquals(monitor.getHits(), 1);
        assertNotNull(resultActivity);

        TextView resultTextView = (TextView) resultActivity.findViewById(R.id.result);
        assertEquals(resultTextView.getText().toString(), "11");
    }
}
```



运行测试

``` 
./gradlew connectedAndroidTest
```

测试结果位于 `build/reports/androidTests/connected/index.html`



#### Espresso

基于 Android Instrumentation 实现，可以减少测试的代码量。

使用方法

修改 `build.gradle` 文件

``` gradle
packagingOptions {
    exclude 'LICENSE.txt'
}
defaultConfig {
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}
dependencies {
    androidTestCompile 'com.android.support.test:runner:0.3'
    androidTestCompile 'com.android.support.test:rules:0.3'
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2'
}
```

Sample

``` java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class EspressoTest {

    @Rule
    public ActivityTestRule<CalculatorActivity> activityTestRule
            = new ActivityTestRule<>(CalculatorActivity.class);

    @Test
    public void testAdd() {
        onView(ViewMatchers.withId(R.id.number1)).perform(typeText("2"));
        onView(ViewMatchers.withId(R.id.number2)).perform(typeText("20"));

        onView(ViewMatchers.withId(R.id.calc)).perform(click());

        onView(ViewMatchers.withId(R.id.result)).check(matches(ViewMatchers.withText("22")));
    }
}
```

运行测试

``` 
./gradlew connectedAndroidTest
```

测试结果位于 `build/reports/androidTests/connected/index.html`



#### Robotium

基于 Android Instrumentation 开发，类似 Espresso。

使用方法

修改 `build.gradle` 文件

``` gradle
defaultConfig {
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}

dependencies {
    androidTestCompile 'com.jayway.android.robotium:robotium-solo:5.4.1'
}
```

Sample

``` java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class RobotiumTest extends ActivityInstrumentationTestCase2<CalculatorActivity> {

    public RobotiumTest() {
        super(CalculatorActivity.class);
    }

    @Before
    @Override
    public void setUp() throws Exception {
        super.setUp();
        injectInstrumentation(InstrumentationRegistry.getInstrumentation());
    }

    @Test
    public void testAdd() {
        Solo solo = new Solo(getInstrumentation(), getActivity());

        solo.enterText(0, "3");
        solo.enterText(1, "30");

        solo.clickOnButton("=");

        solo.assertCurrentActivity("Current is ResultActivity", ResultActivity.class);

        assertThat(solo.searchText("33"), is(true));
    }
}
```

运行测试

``` 
./gradlew connectedAndroidTest
```

测试结果位于 `build/reports/androidTests/connected/index.html



#### UIAutomator

需要Android 4.3以上机器才支持，所以暂时不做考虑



#### Appium

支持 Java， Javascript 等多种编程语言编写测试用例，可以运行在 Android 和 IOS 上，底层实际是调用了对应平台的测试框架。本身搭建测试环境等相比较传统测试方法略微复杂。相比较而言更适合专门的测试人员测试多种平台的功能统一性，不是太适合只进行某一平台开发的开发人员。



### 总结

#### 测试工具

工具             | 用途  |  测试方法   |
:-----------------------: | :---: | :-----: |
 Android Testing |  logic | connect |
 Robolectric    | logic |   local  |
 Instrumentation  |  ui   | connect |
 Espresso      |  ui   | connect |
 Robotium      |  ui   | connect |
 Appium       |  ui   | connect |


#### 断言工具

- Hamcrest
  - junit 依赖的断言工具，语法类似 `assertThat(actual, is(expected))`
- AssertJ   
  - Java平台非常流行的断言工具，square还特别开发了android版本，语法类似 `assertThat(actual).isEqualTo(expected)`

#### Mock 工具

- Mockito
  - 老牌mock工具

- PowerMock
  - 对Mockito的工具进行了扩展，但是与Dexmaker相性并不好

#### CI 服务

- Travis CI
  - Github上最有名的CI服务

- Wercker
  - 支持 Bitbucket

### 选型

**Logic Test**

使用 Robolectric + AssertJ + PowerMock + Travis CI/Wercker

**UI Test**

使用 Espresso + AssertJ + Mockito + Travis CI/Wercker



## 坑

### 发生异常 bad class file magic (cafebabe) or version (0034.0000)

UNEXPECTED TOP-LEVEL EXCEPTION:com.android.dx.cf.iface.ParseException: bad class file magic (cafebabe) or version (0034.0000)

原因：使用了 Java 1.8 进行编译

修复：

Android 项目

``` gradle
compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_7
    targetCompatibility JavaVersion.VERSION_1_7
}
```

引用的 Java 项目

``` gradle
sourceCompatibility = '1.7'
targetCompatibility = '1.7'
```

### Robolectric 报找不到 AndroidManifest.xml 文件

报 "WARNING: No manifest file found at ./AndroidManifest.xml.Falling back to the Android OS resources only.

To remove this warning, annotate your test class with @Config(manifest=Config.NONE)."

修复：

`manifest = "./src/main/AndroidManifest.xml"`

## 参考资料

- [Androidのテスティングフレームワークを選定してみる](http://qiita.com/shikato/items/071a4c879b5c3f0b46f0#espresso)
- [All in Together: Android Studio, Gradle and Robolectric](https://www.bignerdranch.com/blog/all-in-together-android-studio-gradle-and-robolectric/)
