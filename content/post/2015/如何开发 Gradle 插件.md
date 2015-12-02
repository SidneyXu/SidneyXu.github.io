---
comments: true
date: 2015-09-30T07:07:36+08:00
description: ""
draft: false
keywords:
- groovy
- gradle
- plugin
slug: "how-to-develop-gradle-plugin"
tags:
- groovy
- gradle
title: 如何开发 Gradle 插件
toc: true
topics:
- Plugin
---


## 概述

基于 Gradle 进行构建的程序离不开 Gradle 插件。有时现存的插件没有办法满足我们的需求，这时就需要我们自定义 Gradle 插件。本文就是从头编写 Gradle 插件的一个简单例子。

使用的开发环境为 IDEA，开发语言为 Groovy，尽管也可以用 Java 来开发 Gradle 插件，但是开发时配置繁琐且效率低，所以还是推荐使用 Groovy。

<!--more-->

## 开发步骤

### 建立工程

使用 IDEA 建立一个基于 Gradle 构建的工程 `sample-plugin`，并建立子工程 `plugin`。


### 修改子工程的 `build.gradle` 文件

```gradle
apply plugin: 'groovy'

buildscript {
    repositories {
        mavenCentral()
    }
}

repositories {
    mavenCentral()
    mavenLocal()
}

dependencies {
    compile gradleApi()
    compile localGroovy()
    compile 'org.codehaus.groovy:groovy-all:2.3.11'
}
```

### 编写 Plugin 代码

在 plugin 工程下新建包含插件代码的 gradle 文件： `greeting.gradle`

```gradle
apply plugin: GreetingPlugin

greeting {
    message = 'Hi'
    greeter = 'Gradle'
}

class GreetingPlugin implements Plugin<Project> {
    void apply(Project project) {
        // Add the 'greeting' extension object
        project.extensions.create("greeting", GreetingPluginExtension)

        // Add a task that uses the configuration
        project.task('hello') << {
            println "${project.greeting.message} from ${project.greeting.greeter}"
        }
    }
}

class GreetingPluginExtension {
    String message
    String greeter
}
```

以上代码创建了一个名为 `GreetingPlugin` 的插件，该插件包含一个名为 `hello` 的 task。此 Task 负责输出工程的 `project` 对象下的 `greeting.message` 和 `greeting.greeter`。

继续修改 `build.gradle`，导入以上编写的代码

```gradle
apply from: "greeting.gradle"
```

### 运行代码

在 plugin 工程下执行以下语句

```bash
gradle tasks
```

可以看到控制台有输出语句

```
Other tasks
-----------
hello
```

这就是刚才创建的 Gradle Task。接着执行以下语句运行该 Task：

```bash
gradle -q hello
```

成功的话控制台会输出 "Hi from Gradle"。至此一个简单的插件已经开发完毕，接下来为了在其它工程中使用我们可以将插件发布到本地路径。

### 发布插件

#### 编写 Groovy 文件

`greeting.gradle` 文件只是用于快速开发时使用，正式开发或发布时我们需要编写单独的 Groovy 文件。

新建 `GreetingPlugin.groovy` 文件，将之前的 `greeting.build` 中的除了 `apply` 和 `greeting()` 之外的代码都拷贝到该文件。 

#### 指定 Short Plugin Id

使用插件时我们需要指定插件的 id，如 `apply plugin: "Java"`，默认的插件 ID 为包名+类名，这个默认的命名规则太长了，使用时很不方便，我们可以指定一个 Short ID 作为别名。

步骤如下

在 plugin 工程的 `resources` 目录下建立 `META-INF/gradle-plugins` 文件夹，在其中建立名为 `<shortId>.properties` 的文件

例

greeting.properties

```
implementation-class=com.bookislife.example.GreetingPlugin
```

此文件名即为 `implementation-class` 对应的插件的Short ID。

#### 发布插件到本地

修改 plugin 下的 `build.gradle` 文件，追加以下代码

```gradle
group "com.test"
version "1.0-SNAPSHOT"
```

以上指定了插件发布的 group 和 version，而插件的 name 则默认为模块名，即 `plugin`，继续修改 `build.gradle` 追加 maven 发布插件

```gradle
apply plugin: 'maven'

uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: uri('../repo'))
        }
    }
}
```

运行

```bash
gradle -q upload
```

此时编写好的插件就会被发布到工程根目录下的 `repo` 文件夹下。


### 使用插件

在父工程的根目录下建立新的基于 Gradle 的工程 "test"，修改该工程下的 `build.gradle` 文件

```gradle
buildscript {
    repositories {
        maven {
            url uri('../repo')
        }
        mavenCentral()
    }
    dependencies {
        //  group:name:version
        classpath 'com.test:plugin:1.0-SNAPSHOT'
    }
}

apply plugin: 'greeting'

greeting {
    message = 'Hello'
    greeter = 'a test module'
}
```

接着定位到 test 工程下，在控制台执行以下语句

```bash
gradle -q hello
```

成功的话可以看到控制台输出 `Hello from  a test module`。

## Debug 插件

1. 在对应的代码处打上断点
2. 执行需要 Debug 的 task  
	```bash
	gradle -Dorg.gradle.debug=true hello
	```  
3. 选择 IDEA 的 `Run Configurations` -> 建立 `Remote`，创建完后点击 `Debug` 按钮，程序就会在断点处中断运行了。


完整的 Gradle Plugin 示例见 [hello-gradle][https://github.com/SidneyXu/templates/tree/master/hello-gradle]


