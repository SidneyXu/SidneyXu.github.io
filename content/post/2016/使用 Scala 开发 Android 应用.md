---
comments: true
date: 2016-01-10T20:01:39+08:00
description: ""
draft: false
keywords:
- java
- scala
- android
slug: "dev-android-using-scala"
tags:
- java
- scala
- android
title: 使用 Scala 开发 Android 应用
toc: true
topics:
- Dev
---


# Scala Android

## 场景

之前介绍了使用 Groovy 和 Kotlin 开发 Android 程序，本节主要介绍如何使用 Scala 开发 Android 程序。

<!--more-->

## 环境配置

1. 在 IDEA 或 Android Studio 中安装 `Scala` 插件。步骤：打开 `Settings` 或 `Preferences` 选择 `Plugins`，点击 `Browser repositories` 输入 `Scala` 进行安装，安装完后重启 IDE。

2. 建立一个空的 Android 项目

3. 修改 `build.gradle` 文件

    添加 `gradle-android-scala-plugin` 插件

    ```groovy
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:1.2.3'
            classpath "jp.leafytree.gradle:gradle-android-scala-plugin:1.4"
        }
    }
    ```

    添加 plugin

    ```groovy
    apply plugin: "jp.leafytree.android-scala"
    ```

    添加 runtime

    ```groovy
    dependencies {
        compile "org.scala-lang:scala-library:2.11.7"
    }
    ```

    修改 sourceSets

    ```groovy
    android {
        sourceSets {
           main.scala.srcDirs += 'src/main/scala'
        }
    }
    ```

    添加 `scaloid` 作为 Android 的 Scala 扩展

    ```groovy
    dependencies {
        compile "org.scaloid:scaloid_2.11:4.0"
    }
    ```

4. 因为 Scala 环境过大会导致 65535 的错误，所以需要开启混淆。

添加混淆

```
-dontoptimize
-dontobfuscate
-dontpreverify
-dontwarn scala.**
-ignorewarnings
# temporary workaround; see Scala issue SI-5397
-keep class scala.collection.SeqLike {
    public protected *;
}

# this can be omitted if current Android Build target is android-16
-dontwarn org.scaloid.**
```

开启混淆

```groovy
buildTypes {
    debug {
        minifyEnabled true
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    release {
        minifyEnabled true
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
```

完整的 `build.gradle` 文件可以见 [示例][01]。

## 编写代码

### 使用 Scala 的语法

使用 Scala 后可以省略分号，也可以使用更优雅的类型转换方式。

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

Scala 的写法

```Scala
override def getView(position: Int, convertView: View, parent: ViewGroup): View = {
    val view: View = super.getView(position, convertView, parent)
    val title = view.findViewById(android.R.id.text1).asInstanceOf[TextView]
    title.setText(getItem(position))
    view
}
```

### 使用 scaloid

scaloid 中使用了大量的隐式参数和 DSL 语句，使 Android 程序的开发方便不少。

最简单的使用 scaloid 的方法就是继承 `SActivity` 类。

```scala
import org.scaloid.common._

class MainActivity extends SActivity
```

然后就可以使用 scaloid 提供的方法来简化代码

```scala
find[Button](android.R.id.button1).text = "hello world"
```

而不使用此功能的话则需要使用以下代码，麻烦了很多

```java
((TextView)findViewById(R.id.button1)).setText("hello world");
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

```scala
find[Button](android.R.id.button1).onClick((v: View) =>
    startActivity[CountryListActivity]
)
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

Scala 的方式

```scala
//  定义耗时任务
def findCountries(doneCallback: (ArrayBuffer[String], Exception) => Unit):Unit = {
    try {
        //  耗时任务
        doneCallback(results, null)
    } catch {
        case e: Exception => callback(null, e)
    }
}
//  调用该方法
findCountries((names: ArrayBuffer[String], e: Exception) =>
)
```

可以看到使用闭包后无需预先定义回调接口，方便很多。

完整的 Android 示例见 [ScalaAndroid][02]

## 参考资料

- [Github gradle-android-scala-plugin](https://github.com/saturday06/gradle-android-scala-plugin)
- [Github scaloid](https://github.com/pocorall/scaloid)

[01]:   https://github.com/SidneyXu/templates/blob/master/ScalaAndroid/app/build.gradle
[02]:   https://github.com/SidneyXu/templates/tree/master/ScalaAndroid

