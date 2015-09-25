---
comments: true
date: 2015-09-25T11:59:24+08:00
description: ""
draft: false
keywords:
- Robolectric
- Assertj
- Mockito
- PowerMock
- Android
slug: "Robolectric+Assertj+PowerMock-android-logic-test"
tags:
- test
- android
title: "使用Robolectric+Assertj+PowerMock搭建 Android 的逻辑测试环境"
toc:  true
topics:
- Test
---


## 编写 `build.gradle` 文件

``` gradle
dependencies {
    //  Robolectric
    testCompile "org.robolectric:robolectric:3.0"
    
    //  AssertJ 
    testCompile 'com.squareup.assertj:assertj-android:1.1.0'

    //  PowerMock + Mockito
    testCompile 'org.powermock:powermock-module-junit4:1.6.2'
    testCompile "org.powermock:powermock-module-junit4-rule:1.6.2"
    testCompile 'org.powermock:powermock-api-mockito:1.6.2'
    testCompile "org.powermock:powermock-classloading-xstream:1.6.2"
}
```

<!--more-->

## 编写测试代码

``` java
@RunWith(RobolectricGradleTestRunner.class)
@Config(sdk = 21,
        constants = BuildConfig.class, manifest = "build/intermediates/bundles/debug/AndroidManifest.xml")
@PowerMockIgnore({"org.mockito.*", "org.robolectric.*", "android.*"})
@PrepareForTest(Calc.class)
public class RobolectricPowerMockTest {

    @Rule
    public PowerMockRule rule = new PowerMockRule();

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

    @Test
    public void testCalc() {
        PowerMockito.mockStatic(Calc.class);
        Mockito.when(Calc.add(1, 2)).thenReturn(10);

        assertThat(Calc.add(1, 2), is(10));
    }
}
```

## 运行测试

``` 
./gradlew clean testDebug
```

## 查看测试报告

测试结果位于 `build/outputs/reports/tests/debug/index.html`
