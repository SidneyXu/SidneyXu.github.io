---
comments: true
date: 2015-09-25T11:58:50+08:00
description: ""
draft: false
keywords:
- java
- Espresso
- Assertj
- Mockito
- Android
slug: "Espresso+Assertj+Mockito-android-ui-test"
tags:
- test
- android
title: "使用Espresso+Assertj+Mockito搭建Android的UI测试环境"
toc: true
topics:
- Test
---

## 编写 `build.gradle` 文件

``` gradle
android {
    packagingOptions {
        exclude 'LICENSE.txt'
        exclude 'LICENSE'
        exclude 'NOTICE'
        exclude 'asm-license.txt'
    }
    defaultConfig {
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
}

dependencies {
    androidTestCompile 'com.squareup.assertj:assertj-android:1.1.0'
    androidTestCompile 'com.android.support.test:runner:0.3'
    androidTestCompile 'com.android.support.test:rules:0.3'
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2'

    androidTestCompile 'org.mockito:mockito-core:1.9.5'
    androidTestCompile 'com.google.dexmaker:dexmaker:1.2'
    androidTestCompile 'com.google.dexmaker:dexmaker-mockito:1.2'
}
```

<!--more-->

## 编写测试代码

``` java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class EspressoMockTest {

    @Rule
    public ActivityTestRule<CalculatorActivity> activityTestRule
            = new ActivityTestRule<>(CalculatorActivity.class);

    @Test
    public void testAdd() {
        onView(ViewMatchers.withId(R.id.number1)).perform(typeText("2"));
        onView(ViewMatchers.withId(R.id.number2)).perform(typeText("20"));

        onView(ViewMatchers.withId(R.id.calc)).perform(click());

        onView(ViewMatchers.withId(R.id.result)).check(matches(ViewMatchers.withText("22")));
        
        Bean bean = Mockito.mock(Bean.class);
        Mockito.when(bean.getCount()).thenReturn(100);
        Assertions.assertThat(bean.getCount()).isEqualTo(100);
    }
  
}
```

## 运行测试

``` 
./gradlew connectedAndroidTest
```

## 查看测试报告

测试结果位于 `build/outputs/reports/androidTests/connected/index.html`


