---
comments: true
date: 2015-11-30T10:04:55+08:00
description: ""
draft: false
keywords:
- java
- groovy
- android
slug: "dev-android-using-groovy"
tags:
- java
- groovy
- android
title: "使用 Groovy 开发 Android 应用"
toc: true
topics:
- Dev
---

# Groovy Android

## 场景

我们都知道 Android 原生应用基本都是使用 Java 开发的，但是实际上 Groovy 也是运行在 JVM 上的，所以也可以用于开发 Android 应用程序，只是需要做一些额外的准备工作。

<!--more-->

## 环境配置

1. 既然是使用 Groovy 语言进行开发，所以第一步就是在 IDEA 或 Android Studio 中安装 `Groovy` 插件。步骤：打开 `Settings` 或 `Preferences` 选择 `Plugins`，点击 `Browser repositories` 输入 `Groovy` 进行安装，安装完后重启 IDE。

2. 建立一个空的 Android 项目

3. 修改 `build.gradle` 文件

    添加 `gradle-groovy-android-plugin` 插件

    ```groovy
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:1.2.3'
            classpath 'org.codehaus.groovy:gradle-groovy-android-plugin:0.3.6'
        }
    }
    ```

    添加 plugin

    ```groovy
    apply plugin: 'groovyx.grooid.groovy-android'
    ```

    添加 runtime

    ```groovy
    dependencies {
        compile 'org.codehaus.groovy:groovy:2.4.5:grooid'
        // 如果需要使用  Groovy 的 JSON Api 的话还需要配置下面的依赖
        compile('org.codehaus.groovy:groovy-json:2.4.5') {
            transitive = false
        }
    }
    ```

    3. 配置 packagingOptions

    ```groovy
    packagingOptions {
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/DISCLAIMER'
        exclude 'META-INF/groovy-release-info.properties'
    }
    ```

完整的 `build.gradle` 文件可以见 [示例][01]。

4.配置 proguard

```
-dontobfuscate
-keep class org.codehaus.groovy.vmplugin.**
-keep class org.codehaus.groovy.runtime.dgm*
-keepclassmembers class org.codehaus.groovy.runtime.dgm* {
    *;
}

-keepclassmembers class ** implements org.codehaus.groovy.runtime.GeneratedClosure {
    *;
}

-dontwarn org.codehaus.groovy.**
-dontwarn groovy**
```


## 编写代码

### 使用静态编译

如果不需要使用动态编译的话，可以开启静态编译以获得更好的性能，使用方法就是在类上添加 `@CompileStatic` 注解。开启静态编译后一些正常的 Groovy 代码可能无法正常运行，因为静态编译会对类型进行严格的检查，所以代码风格可能需要做出一定改变。

```groovy
@CompileStatic
class MainActivity extends AppCompatActivity
```

### 使用 Groovy 的语法

使用 Groovy 后可以省略方法的返回值，省略分号，也可以使用更优雅的类型转换方式。

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

Groovy 的写法

```groovy
@Override
View getView(final int position, final View convertView, final ViewGroup parent) {
    View view = super.getView(position, convertView, parent)
    def title = view.findViewById(android.R.id.text1) as TextView
    title.setText(getItem(position))
    view
}
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
findViewById(android.R.id.button1).onClickListener = {
    startActivity(new Intent(this, CountryListActivity.class))
}
```

可以看到使用闭包替代的话代码可以简单不少，不过使用闭包后如果闭包中碰到需要调用当前 Activity 的上下文时比较难处理，需要注意 `this`，`owner`，`delegate` 的区别。

### 使用闭包替代回调

由于 Android 的机制，网络请求必须放在其它的线程中执行，所以使用 Java 编程时通常都需要定义各种回调接口用于通知耗时任务的完成。

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

Groovy 的方式

```groovy
//  定义耗时任务
void findCountries(Closure doneCallback) {
    try {
        //  耗时任务
        doneCallback(results, null)
    } catch (e) {
        doneCallback(null, e)
    }
}
//  调用该方法
findCountries{ List<String> results, Exception e ->
});
```

可以看到使用闭包后无需预先定义回调接口，方便很多。

完整的 Android 示例见 [GroovyAndroid][02]

[01]:   https://github.com/SidneyXu/templates/blob/master/GroovyAndroid/app/build.gradle
[02]:   https://github.com/SidneyXu/templates/tree/master/GroovyAndroid
