---
comments: true
date: 2016-04-11T10:41:57+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "how-to-dev-android"
tags:
- android
title: 如何写出低碳环保的 Android 代码
toc: true
topics:
- Dev
---


随着环境问题越来越严重，人们越来越重视低碳环保的生活方式。作为码农的我们自然也应该为环保做出应有的贡献。那么什么是低碳环保，简而言之就是就是低能量、低消耗、低开支的生活方式，映射到我们的工作中就是以最低的消耗的来完成组织交给我们的任务。

<!--more-->

以下就以 Android 开发为例从库和语言两方面来讨论如何实现低碳环保的编程方式。

## 从第三方库来说

充分利用现存资源，尽可能不重复造轮子。从以往来看，如果你对现存的轮子有各种不满试图从头写，那么最终结局中可能性最大的就是只写了部分后直接放弃转而成为某个轮子的支持者，写完且比现有轮子要好的可能性还不如转行去大学城门口卖炒面。当然如果你写的是就是 SDK 之类的基础工具，那还是尽量减少依赖为好。

那么该如何挑选第三方库呢？Android 的应用层开发虽然使用 Java 语言，但并不是 Java 上的库都适合 Android 开发。Android 使用的不是 Oracle JDK 也不是 Open JDK，而是 Google 改写过的 Apache Harmony JDK，很多 Oracle JDK 自带的类（特别是 javax 包下的）在 Android 中并不存在，所以使用这些方法的库 Android 不能使用。

此外 Android 存在 65536 问题，这个坑体现在以下两点：

1）Android 机器在应用的安装过程中，系统会运行 `dexopt` 工具，将 `.dex` 文件优化为 `.odex` 文件，其中 `dexopt` 工具使用了固定的缓冲区大小来保存方法的元信息，低版本的 Android 机器上该缓冲区非常小，所以一旦方法数过多会直接导致  `dexopt` 崩溃，应用无法运行。

2）Dalvik 指令集对于一个 `.dex` 文件只能保存 65536 个方法的索引，所以一个 `.dex` 文件即使可以拥有很多方法，但是那些多余的方法也都是无法运行的。详细信息可以阅读官方的 [dalvik-bytecode](https://source.android.com/devices/tech/dalvik/dalvik-bytecode.html) 的 `invoke-kind {vC, vD, vE, vF, vG}, meth@BBBB` 条目。

因此选择 Android 的第三库需要严格注意控制方法的总数量。

### Android 常用库 vs J2EE 常用库

以下我总结了一些 J2EE 和 Android 上的常用库的对比以供参考，使用这些库可以有效提供编程效率，减少能量消耗：

|  功能 |  J2EE |   Android | 备注
| :------------- | :------------- | :------------- | :------------- |
| JSON 解析      | [Jackson](http://wiki.fasterxml.com/JacksonHome)       | [Gson](https://github.com/google/gson)       |  Jackson 功能全面，但太大；Gson 速度一般但胜在体积    |
| Restful       | [Jersey](https://jersey.java.net/)       | [Retrofit](https://github.com/square/retrofit)       |  Jersey 面向服务端，符合 JAX-RS 标准， Retrofit 面向客户端，不符合 JAX-RS 标准    |
|  依赖注入       | [Guice](https://github.com/google/guice)      | [Dagger](https://github.com/square/dagger)       |  Guice 使用反射，Dagger 使用预编译，效率不是一个等级的     |
| NoSQL       | [MongoDB](https://www.mongodb.org)      | [Realm](https://realm.io/cn/docs/java/latest/)      |  Realm 兼容 Android，iOS，ReactNative，比起 Sqlite3 快得多     |
|  单元测试       | [JUnit](https://github.com/junit-team/junit4)      | [Robolectric](http://robolectric.org/) + [Espresso](https://google.github.io/android-testing-support-library/docs/espresso/)      |  JUnit 只能运行在 JVM 上，Robolectric 使 Android 代码可以运行在 JVM 上，Espresso 简化 UI 测试流程（虽然很多情况下 UI 测试没什么用）     |
| 异步调用      | CompletableFuture/[RxJava](https://github.com/ReactiveX/RxJava)      | RxJava + [RxAndroid](https://github.com/ReactiveX/RxAndroid) + [RxLifecycle](https://github.com/trello/RxLifecycle)     |   三合一基本是标配，除此之外还可以加上 [RxBinding](https://github.com/JakeWharton/RxBinding)，[RxPermissions](https://github.com/tbruyelle/RxPermissions)，[RxAndroidAudio](https://github.com/Piasy/RxAndroidAudio) 等等组成完整的 Rx 大家族   |
| 网络请求    | - | [OkHttp](https://github.com/square/okhttp)/[Volley](https://android.googlesource.com/platform/frameworks/volley)| OkHttp 功能强大，Fluent API 可以写出更优秀的代码；Volley 代码量小容易扩展，有非常优秀的队列机制 |
| 时间处理    | Java 8/[Joda-Time](https://github.com/JodaOrg/joda-time) | - | Java 原生时间处理 API 非常糟糕，因此 Java 8 直接加入了 Joda-Time。Joda-Time 虽然易用，但一个只是处理时间的库有 5000 多方法对于 Android 显然不实际，尽管有 joda-time-android 库但通常 Android 端需要处理时间代码不多，建议还是直接调用难用的原生 API |

### 其它方法

- 构建良好的 Android 架构，尽量将 Context 相关的一切和业务逻辑进行分离，使业务逻辑能够脱离于 UI 组件进行本地测试。MVC，MVP,MVVM,Flux 之类的只有适合自己才是最好的。也可以参考下 Google 最近开始编写的 Android 架构的[示例代码](https://github.com/googlesamples/android-architecture)，不得不说 Google 这一步做得是实在有点晚。

- 引入 Fragment 后 Android 应用的[生命周期](https://github.com/xxv/android-lifecycle) 过于恐怖，所以尽管 Google 提倡使用 Fragment，但还是能少用就尽量少用。

- 使用 Timber + Hugo 记录 Log 信息而不是使用原生的 Log 工具，这样无需再自己拼接类名，方法名，参数名和参数值，也不用为了使 Log 更容易被识别加上一堆 `=========afafaf=============` 或者 `~~~~~~~~~~~~~~~~~~~~~` 这样的提示符。

- 使用 Android DataBinding，尽管你不一定喜欢它的数据绑定方式。但是使用了 DataBinding 后你无需调用 `findViewById` 后再强制进行类型转换，也不用使用 Butter Knife 之类的库编写各种注解。

- 对于图像加载注重质量和包大小可以使用 Picasso，注重加载速度或者需要支持 GIF 类型和大图片可以使用 Google 人出品的 Glide。除此之外还有老牌的 Universal Image Loader 和相对较新的 Facebook 出品的 Fresco（Fresco 在这里面是重量级选手，无论是功能还是体积，刚推出时坑不少，还有非常严重的内存泄露，目前该库已经作为 React Native 的图片加载库，不知道这些问题都解决了么）

- 使用 IDEA Live Template 保存常用的类或方法的模板，这是我最常使用的方式，这样有时甚至可以减少近一半的工作量。

- 强制竖屏，Android 上除了视频播放和游戏大部分情况下竖屏足以，根据 2-8 法则很多时候专门适配横屏是很大的资源浪费

## 从语言本身来说

以上的方法在实现低碳环保的功能上还是非常有限的，所以还有种方法就是直接从最根本的语言层面进行着手。

### 使用 Lambda 表达式

使用 Lambda 表达式可以省去不少代码，可惜直到 Java 8 才支持，索性的是 Android 上有一些解决方法。

#### 使用 Retrolambda Gradle Plugin

[Retrolambda Gradle](https://github.com/evant/gradle-retrolambda) 插件可以在编译时通过字节码转换使 Android 能够使用 Lambda 表达式。

#### 开启 Jack 与 Jill

在介绍 Jack 与 Jill 之前先来看看 Android 那坑爹的构建系统，以下图片来自 Google 官方文档，请注意这只是一个大纲，而且有些过时，实际更复杂，想想如果没有 IDE 自己手动敲的痛苦吧：

![A Detailed Look at the Build Process](http://7xlqqp.com1.z0.glb.clouddn.com/android%20build.png)

而 Jack 与 Jill 就是在 Android M 时 Google 为了简化以上流程而推出的构建工具。

以前的主要流程

为了减小 I/O 读取的次数，dx 工具将所有 `.class` 文件合并成 `.dex` 文件

```
javac (.java --> .class) --> dx (.class --> .dex)
```

使用 Jack 与 Jill

Jill 将第三方 `.class` 文件和 `.jar` 文件转换为 `.jayce` 文件。
Jack 将 `.java` 和 `.jayce` 文件合并后转换为优化过的 `.dex` 文件。

```
Jack (.java --> .jack --> .dex)
Jill (.class --> .jayce)
```

当然 Jack 实际是一个工具链，除了以上功能 Jack 还包含了 multidex, proguard 等大量功能，直接替代了原来构建过程中的很多工具。

使用 Jack 与 Jill 还有一个吸引人的特点是可以在今年将发布的 Android N 平台使用上大部分 Java 8 的功能。感兴趣的人可以现在就去尝试一下，但是需要注意的是 IDE 必须大于 Android Studio 2.1 (preview)，SDK 平台必须为 Android N Preview SDK。

此外由于 Java 8 提供的 Lambda 表达式实际就是通过函数式接口实现的，所以在使用 Jack 与 Jill 后这一功能也可以直接使用在 Android N 以前的平台，而不用使用 Retrolambda 这些第三方工具（当然其它的 Java 8 功能都不支持）

目前来说 Jack 与 Jill 有一个很大的缺点就是速度较慢，不支持 Instant Run。此外由于不生成中间状态的字节码文件，所以开启 Jack 与 Jill 后基于字节码的各种工具（如 JaCoCo， Mockito）都将无法使用。（注：Gradle 插件在 1.5 版本提供了 Transform API，可以让我们直接对生成的 dex 文件进行处理，但是目前 Jack 与 Jill 不支持，所以使用该种方式 JaCoCo 仍然无法使用）

### 换种语言

相比较 Lambda 表达式也许换种语言是种更有效的方法。以下就介绍一下适用于 Android 开发的其它语言，当然这并不是说真的需要在实际工作中应用。更多得是因为如果你只会用锤子，那你眼里的所有东西都是钉子，换种语言是为了开拓思路，了解在其它语言中是如何实现同样的功能的。

### Java + Native

该方案以 Java 代码为主，以少量 Native 代码为辅。主要有两种实现方式：

**Java + C/C++**

很明显这种方法非常不环保，基于 NDK 的开发非常复杂，出了错误也不好调试，目前这种方式主要用于 Cocos2D-X 这种游戏引擎。

**Java + Go**

Go 从 1.5 版本开始同时支持 Android 和 iOS 开发。由于 Go 是 Google 亲儿子所以 1.5 出来时大家对未来都非常期待。不过至今为止实际发展非常缓慢，文档非常稀少。相比较使用 C/C++ 方式使用 Go 有这么几个特点：

优点：

- 语法简单，开发迅速，不用写头文件，不用写 Makefile，不用手动写 Native 方法，借助插件 Go 代码会被直接编译成包含 `.so` 文件的 `.aar` 库，导入 Android 工程后就像写原生 Java 代码一样直接使用即可。

缺点：

- 文档奇缺，发展缓慢剧。
- 目前只支持 arm 架构。
- 有 Bug，我写的代码编译版本选择 API 22 正常运行，选择 API 23 上直接奔溃。


### Native + Java

该方案以 Native 代码为主，以少量 Java 代码为辅。最大的特点就是提倡以同一种语言为不同平台编写不同代码，而不是一套代码到处运行。主要代表有以下几种：

**RubyMotion**

RubyMotion 由 MacRuby 的开发者发明，可以通过 Ruby 代码编写 iOS 和 Android 代码。

优点：

- 提供了 Android 上的几乎所有的 API 的 Ruby 实现，也可以直接使用 Java 库。

缺点：

- iOS 还有些文档，Android 方面则没有任何像样的官方文档。导致我在写的时候需要打开 IDEA 先用 Java 写一遍大致框架然后再用 Ruby 进行改写。
- 不支持直接调用 Java 代码，必须使用打包好后的 Java 库。
- 除了官方发布的包，不支持其它任何 Gem 上的包。
- 由于没有钱付正式版，所以我使用的是试用版，使用时不但难以调试，速度慢，而且还 Bug 满天飞，完全没有写 Ruby 的爽快感，正式版估计会好点。

**ReactNative**

ReactNative 由 Facebook 在去年发布，一经发布瞬间成为 Github 上的网红。可以使用 JavaScript 进行代码的编写以及动态更新是其最大特点。然而相比较 iOS 来说 Android 版本并没有发布多少时间，目前还有不少问题。由于最近在看 ReactNative 所以这里写得稍微详细点：

优点：

- 使用 JavaScript 编写，ES 6 语法对于 Java 程序员可能更有亲和力。
- Flex 布局和 JSX 语法和 Android 原生布局方式非常相近，很多属性几乎就是换个名字，容易上手。
- 支持 HotLoad，写个界面刷一下就行了，Android 开发者终于不用忍受 Gradle 那漫长的编译过程。

缺点：

- 目前只是 0.2x 版本，不稳定，并且 React Native 本身使用了不少已经标示为废弃的 API， API 有很大可能会大更新。
- 如果碰到框架本身 Bug，基本没有修复的可能性，只能被动等待官方出解决方案或者切换为 Java 平台。
- 原生 Android 开发就有各种兼容性问题，特别是对于国内小米，华为，魅族等平台，国外的 React Native 是否能处理得好不得而知。
- ReactNative 框架自身绑定了不少第三方库，虽然这些库都挺有名的，但也不能保证人人喜欢，人人用得到。
- 没有重用机制，导致 ListView 效率问题。
- 没有布局管理器，组件间嵌套严重，实际代码中会有大量 `<View>` 嵌套 `<View>` 的情况，不适合写复杂的布局。
- Android 有多种类型 Resource 文件，还有 10 多种限定修饰符，React Native 基本都无法使用，意味着在面对屏幕适配，i18n，切换主题等问题时会非常蛋疼。
- 目前只支持老式的 Drawable 目录下的图片资源，不支持 5.0 以后的 Mipmap 目录下的资源。
- Flux 和在其基础上的 Redux 架构与 Android 传统开发理念不符，很难吸引到广大码农。



### 其它 JVM 语言

相比较以上方案，使用其它 JVM 语言的最大特点就是可以和 Java 进行无缝切换，开发方式上没有什么变更，很容易让人接受。

#### Groovy

Groovy 官方从 2.4 开始就支持了 Android 开发。Groovy 本身是动态语言，效率较低。但是可以通过开启静态编译来提高效率。由于 Groovy 在处理 XML 方面是一绝，所以如果你的应用服务器是基于 SOAP 的话，那么使用 Groovy 替换 Java 无疑是更好的选择，原生的 DOM，SAX，PULL 方式使用起来都太痛苦了。

替换成 Groovy 后最大的优点就是上手没有任何什么难度， 毕竟所有 Android 开发者都写过 Groovy （`build.gradle` 实际就是 Groovy 源文件），而缺点就是编译速度会更慢。

#### Scala

Android 应用中常常需要将上下文传来传去，所以很多人都会在每个 Activity 写上 `mContext = this` 这样没营养的代码。而使用了 Scala 后借助隐式参数 + 隐式转换 + 隐式类这三兄弟就不用写这些没有营养的代码。除此之外借助 Scala 各种 FP 特性代码量也可以得到大量减少。

但是替换成 Scala 后有一个致命的弱点就是主流的 Scala 的 2.11.x 版本核心方法多达 5w。这意味着加上 Android 的原生代码，即使你一行代码没写，仅仅是集成 Scala 运行环境后就超过了 65536 的限制。即使开启了 proguard 删除掉那些不用的代码这个问题也没办法根本解决，所以或许将 Scala 搬到 Android 平台并不是一个好的解决方案。

#### Kotlin

Kotlin 是 JetBrain 研发的一门运行在 JVM 上的语言，官方支持 Android 开发，语法和其之后发布的 Swift 非常相似，所以也有人开发出了 Kotlin 2Swift 的工具，详细对比可以见[Swift is like Kotlin](https://nilhcem.github.io/swift-is-like-kotlin/)。Kotlin 的语法可以看做是 Scala++--，其语法借鉴了 Scala，但是也去除了 Scala 中大量复杂的概念。

在所有 JVM 语言中，目前个人最推荐使用 Kotlin 进行 Android 开发。有以下几个原因：
- Kotlin 由 JetBrain 开发，所以对 Android Studio 有很好的支持。
- Android 界举足轻重的 Jake Wharton 大神和其所在的公司 Square 都很欣赏 Kotlin，将一些 Android 库改写为了 Kotlin 版本。
- Google Android 项目组也对 Kotlin 感兴趣，目前我们常使用的 Databinding 的编译器就是 Kotlin 写的。
- Kotlin 的运行库只有不到 7000 个方法，这意味着它比 v4 还要小。
- Google 目前和 Oracle 的官司越演越烈，以前有传言 Google 会使用 Go 作为 Android 的一类语言，但是目前从发展速度来看可能性很小。近日又有传言 Google 会使用 Swift 来代替 Java，个人觉得相比较而言不如说 Kotlin 可能性更大。（补：写完这段不久后 Swift 就仓库就出现了 For Android 的 Pull Request，看了下这玩意是基于 JNI 的，属于上面说的 Java + Native 的开发方式，对于编写应用来说用处不大，不过相信会被不少人炒作一段时间）
- Kotlin 学习 Clojure 也分为 Kotlin on JVM 和 Kotlin on JavaScript 两个版本。其中 Kotlin on JavaScript 目前内置了 JQuery，但是本身功能很弱，只能写些原始的 JS 代码。如果发展起来的话，说不定将来可以用于编写 ReactNative 代码。
- Kotlin 没有什么历史负担，增加新特性时无需像 Java 一样思考再三。以下为 Kotlin 目前的 RoadMap，可以看到诸如协程之类的功能 Kotlin 都会在语言层面实现，而不用像 Java 一样必须依赖 Quasar 这种第三方库在字节码方面做文章才行。如果等 Java 实现的话搞不好需要等到 Java 20.

![Kotlin RoadMap](http://7xlqqp.com1.z0.glb.clouddn.com/kotlin%20roadmap.png)

就我个人开发中常使用的 Kotlin 功能有这么几种：
- 方法扩展，该功能可以给已存在的类添加方法，本质上其实现类似 Scala 中的隐式类。所以你可以直接给 Activity 添加 toast, alert 功能。
- 使用 DSL 语句来编写界面
- internal 访问权限。我们知道 Java 中包与包是没有任何关系的，这意味这 a.b 和 a.b.c 实际是两个包。所以在分层时 a.b.c 中不得不暴露大量的 public 方法给 a.b 包中所在的类。而在 SDK 开发中为了有良好的封装性，尽量暴露更少的接口，所以往往不得不将大部分类都放在同一个包中，然后通过 default 访问权限来限制外部访问。类少的时候还能够忍受，类的一多的话就会非常混乱。Kotlin 中的 internal 访问权限可以限制只能属于同一个模块中的类进行访问，其它模块无法访问。那么什么是模块？在 Kotlin 中就是一个 jar 包，所以这功能对 SDK 来说就是神器。
- Kotlin Android Extension。使用后无需修改任何代码，直接就可以在代码中使用 Xml 中声明的任何的控件。


最后附上一段简单的 kotlin 代码

```kotlin
relativeLayout {
    textView {
        id = android.R.id.text1
        text = "Loading..."
    }.lparams {
        centerInParent()
    }
    editText {
        id = android.R.id.edit
        hint = "Page Count for retain"
        inputType = InputType.TYPE_NUMBER_VARIATION_NORMAL
    }.lparams {
        below(android.R.id.text1)
        centerInParent()
    }
    button("click") {

    }.lparams {
        below(android.R.id.edit)
        centerInParent()
    }.onClick {
        println("hello world")
    }
}.style {
    when(it){
        is TextView -> it.textSize = 20f
    }
}
```

---

以上 Go 和 Ruby 编写 Android 应用的示例可以见 [AndroidDemoInOtherLanguages](https://github.com/SidneyXu/AndroidDemoInOtherLanguages)。其它 JVM 语言编写 Android 应用的示例可以见 [AndroidDemoIn4Languages](https://github.com/SidneyXu/AndroidDemoIn4Languages)。

扯了这么多可能有很多人觉得就这些怎么可能实现低碳环保的编程。没错，你想的很对，要实现低碳环保的编程方式说到底只有唯一一个有效的方法就是说服你的项目经理或者其他有话语权的人取消或修改掉那些不环保的需要，仅此而已。本次扯淡到此为止。

本文作者来自 MaxLeap 游民：SidneyXu









