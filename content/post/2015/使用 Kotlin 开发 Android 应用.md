---
comments: true
date: 2015-11-30T17:46:07+08:00
description: ""
draft: false
keywords:
- java
- kotlin
- android
slug: "dev-android-using-kotlin"
tags:
- java
- kotlin
- android
title: "使用 Kotlin 开发 Android 应用"
toc: true
topics:
- Dev
---

# Kotlin Android

## 场景

之前介绍了使用 Groovy 开发 Android 程序，本节主要介绍如何使用 Kotlin 开发 Android 程序。

<!--more-->

## 环境配置

1. 在 IDEA 或 Android Studio 中安装 `Kotlin` 插件。步骤：打开 `Settings` 或 `Preferences` 选择 `Plugins`，点击 `Browser repositories` 输入 `Kotlin` 进行安装，安装完后再安装 `Kotlin Extensions For Android`，安装完后重启 IDE。

2. 建立一个空的 Android 项目

3. 修改 `build.gradle` 文件

    添加 `kotlin-gradle-plugin` 插件和用于扩展 Android 功能的 `kotlin-android-extensions` 插件。

    ```groovy
    buildscript {
        ext.kotlin_version = '1.0.0-beta-2423'
        repositories {
            jcenter()
            mavenCentral()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:1.2.3'
            classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
            classpath "org.jetbrains.kotlin:kotlin-android-extensions:$kotlin_version"
        }
    }
    ```

    添加 plugin

    ```groovy
    apply plugin: 'kotlin-android'
    ```

    添加 maven 仓库，Kotlin 相关依赖都保存在 maven 中央仓库中

    ```groovy
    repositories {
        jcenter()
        mavenCentral()
    }
    ```

    添加 runtime

    ```groovy
    dependencies {
        compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    }
    ```

    修改 sourceSets

    ```groovy
    android {
        sourceSets {
           main.java.srcDirs += 'src/main/kotlin'
        }
    }
    ```

完整的 `build.gradle` 文件可以见 [示例][01]。

## 编写代码

### 使用 Kotlin 的语法

使用 Kotlin 后可以省略分号，也可以使用更优雅的类型转换方式。

Java 的写法

```java
@Override
View getView(final int position, final View convertView, final ViewGroup parent) {
    View view = super.getView(position, convertView, parent);
    TextView title = view.findViewById(android.R.id.text1);
    title.setText(getItem(position));
    return view;
}
```

Kotlin 的写法

```kotlin
override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View? {
    val view = super.getView(position, convertView, parent)
    val title = view.findViewById(android.R.id.text1) as TextView
    title.text = getItem(position)
    return view
}
```

### 使用 Kotlin Extension

Kotlin Extension 类似 Android 新加入的 DataBinding，可以直接对控件进行引用，但是使用起来更加方便。

比如说在 `activity_main.xml` 中定义了一个按钮，Kotlin Extension 会生成包含整个 Layout 内容的类 `kotlinx.android.synthetic.activity_main`，为了方便起见，在文件头可以全部引入其内容：

```kotlin
import kotlinx.android.synthetic.activity_main.*
```

之后就可以直接对其进行引用

```kotlin
button1.text = "hello world"
```

而不使用此功能的话则需要使用以下代码，麻烦了很多

```java
((Button)findViewById(R.id.button1)).setText("hello world");
```

### 使用闭包替代匿名类

匿名类的写法

```java
findViewById(android.R.id.button1).setOnClickListener(new View.OnClickListener() {
    @Override
    void onClick(final View v) {
        startActivity(new Intent(MainActivity.this, CountryListActivity.class))
    }
})
```

闭包的写法

```groovy
button1.setOnClickListener {
    startActivity(new Intent(this, CountryListActivity.class))
}
```

### 使用闭包替代回调

同 Groovy 一样，使用闭包替代回调也是一种通常的做法。

Java 的方式

```java
//  定义回调接口
public interface FindCallback {
    void onFinish(List<String> results, Exception e);
}
//  定义耗时任务
void findCountries(FindCallback doneCallback) {
    try {
        //  耗时任务
        doneCallback.onFinish(results, null);
    } catch (Exception e) {
        doneCallback.onFinish(null, e);
    }
}
//  调用该方法
findCountries(new FindCallback(){
    void onFinish(List<String> results, Exception e){
    }
});
```

Kotlin 的方式

```kotlin
//  定义耗时任务
void findCountries(doneCallback: (List<String>?, Exception?) -> Unit) {
    try {
        //  耗时任务
        doneCallback(results, null)
    } catch (e: Exception) {
        doneCallback(null, e)
    }
}
//  调用该方法
findCountries{ list, e ->
})
```

可以看到使用闭包后无需预先定义回调接口，方便很多。

### 使用 Kotlin Function Extension

通常我们会将很多方法放在工具类里，不过通过 Kotlin 的方法扩展功能可以直接将部分功能移到 `Activity` 中。

例：为 `Activity` 增加 `Toast` 功能

增加方法扩展

```kotlin
public fun Activity.toast(message: CharSequence?, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this, message, duration).show()
}
```

之后在 `Activity` 中就可以调用以下代码直接呼出 `Toast`。

```kotlin
toast(message)
```

例：为 `Activity` 添加画面跳转功能

增加方法扩展

```kotlin
inline public fun <reified T : Activity> Activity.navigate(bundle: Bundle? = null) {
    val intent = Intent(this, T::class.java)
    if (bundle != null) {
        intent.putExtras(bundle)
    }
    startActivity(intent)
}
```

之后在 `Activity` 中就可以调用以下代码直接进行画面跳转。

```kotlin
navigate<CountryListActivity>()
```



完整的 Android 示例见 [KotlinAndroid][02]

[01]:   https://github.com/SidneyXu/templates/blob/master/KotlinAndroid/app/build.gradle
[02]:   https://github.com/SidneyXu/templates/tree/master/KotlinAndroid

