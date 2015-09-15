---
comments: true
date: 2015-09-15T06:59:50+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-03-maven-project"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 03.创建 Maven 工程
toc: true
topics:
- JGSK
---

## 概述

在进行各种测试之前，我们先为之后的所有例子建立一个完整的工程项目。我使用的开发工具为 IDEA 14 CE，构建工具选择最为通用的 Maven。
<!--more-->

### 建立工程

首先打开 IDEA，选择 `File` -> `New` -> `Project`

![][01]

在弹出的对话框左侧选择 `Maven`，然后选择 `Next`，进入下一个界面。

![][02]

接着在 `GroupId` 一栏填入 "com.bookislife"，在 `ArtifactId` 一栏填入 "jgsk"，继续 `Next`

![][03]

`Project name` 为 "JGSK"，`Project location` 任意，点击 `Finish` 完成工程创建。

![][04]

然后在工程的根目录下执行以下命令创建各目录

```zsh
➜  JGSK git:(master) ✗ mkdir -p src/main/java/com/bookislife/jgsk/java
➜  JGSK git:(master) ✗ mkdir -p src/main/groovy/com/bookislife/jgsk/groovy
➜  JGSK git:(master) ✗ mkdir -p src/main/scala/com/bookislife/jgsk/scala
➜  JGSK git:(master) ✗ mkdir -p src/main/kotlin/com/bookislife/jgsk/kotlin
➜  JGSK git:(master) ✗ mkdir -p src/test/java/com/bookislife/jgsk/java
➜  JGSK git:(master) ✗ mkdir -p src/test/groovy/com/bookislife/jgsk/groovy
➜  JGSK git:(master) ✗ mkdir -p src/test/scala/com/bookislife/jgsk/scala
➜  JGSK git:(master) ✗ mkdir -p src/test/kotlin/com/bookislife/jgsk/kotlin
```

### 安装插件

在编写程序前，还需要为 IDEA 安装几个插件，安装步骤如下

选择 `Settings (Mac 平台为 Preferences)` - `Plugins` - `Browse repositories`，在弹出的对话框中的输入框中输入以下几个并安装，安装完毕后重启 IDEA

![][06]

需要安装的插件

- Scala
- GMavenPlus IntelliJ Plugin
- Groovy
- Kotlin

### 修改 Pom.xml 文件

接着打开工程根目录下的 `pom.xml` 文件，填入以下内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.bookislife</groupId>
    <artifactId>jgsk</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.encoding>UTF-8</project.build.encoding>
        <jdk.version>1.8</jdk.version>
        <maven-compiler-plugin.verion>3.2</maven-compiler-plugin.verion>
        <maven-resources-plugin.version>2.7</maven-resources-plugin.version>
        <groovy.version>2.4.4</groovy.version>
        <gmavenplus-plugin.version>1.5</gmavenplus-plugin.version>
        <scala.version>2.11.5</scala.version>
        <scala-maven-plugin.version>3.2.0</scala-maven-plugin.version>
        <kotlin.version>0.12.1230</kotlin.version>
    </properties>

    <repositories>
        <repository>
            <id>sonatype.oss.snapshots</id>
            <name>Sonatype OSS Snapshot Repository</name>
            <url>http://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>sonatype.oss.snapshots</id>
            <name>Sonatype OSS Snapshot Repository</name>
            <url>http://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>

    <dependencies>
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-all</artifactId>
            <version>${groovy.version}</version>
        </dependency>

        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>${scala.version}</version>
        </dependency>

        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-stdlib</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.verion}</version>
                <configuration>
                    <source>${jdk.version}</source>
                    <target>${jdk.version}</target>
                    <encoding>${project.build.encoding}</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>${maven-resources-plugin.version}</version>
            </plugin>
            <plugin>
                <groupId>org.codehaus.gmavenplus</groupId>
                <artifactId>gmavenplus-plugin</artifactId>
                <version>${gmavenplus-plugin.version}</version>
                <executions>
                    <execution>
                        <phase>process-sources</phase>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                        <configuration>
                            <sources>
                                <source>
                                    <directory>src/main/groovy</directory>
                                </source>
                            </sources>
                            <testSources>
                                <source>
                                    <directory>src/test/groovy</directory>
                                </source>
                            </testSources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>${scala-maven-plugin.version}</version>
                <executions>
                    <execution>
                        <phase>process-sources</phase>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                        <configuration>
                            <sourceDir>src/main/scala</sourceDir>
                            <testSourceDir>src/test/scala</testSourceDir>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.jetbrains.kotlin</groupId>
                <artifactId>kotlin-maven-plugin</artifactId>
                <version>${kotlin.version}</version>
                <executions>
                    <execution>
                        <id>compile</id>
                        <phase>process-sources</phase>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                        <configuration>
                            <sourceDirs>
                                <source>src/main/kotlin</source>
                            </sourceDirs>
                        </configuration>
                    </execution>
                    <execution>
                        <id>test-compile</id>
                        <phase>process-test-sources</phase>
                        <goals>
                            <goal>test-compile</goal>
                        </goals>
                        <configuration>
                            <sourceDirs>
                                <source>src/test/kotlin</source>
                            </sourceDirs>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

修改完 `pom.xml` 后进行保存。接着选择该 `pom.xml` 文件，单击右键选择 `Maven` - `Reimport`，执行完成后左边工程的 `main` 下的目录文件夹名应该除了 `kotlin` 之外都变成了蓝色，`test` 下除了 `kotlin` 之外都变成了绿色。

为了使 IDEA 也认出 `kotlin` 文件夹，需要进行以下操作：

选择 `File` - `Project Structure...`，在弹出的对话框中选择 `Modules` 条目，选择 `main/kotlin` 目录并点击上方蓝色的 `Sources` 按钮，接着选择 `test/kotlin` 目录并点击上方绿色的 `Tests` 按钮，点击 `OK` 关闭对话框。

![][07]

此时，整个工程结构就创建完成了。

---

项目源码见 [JGSK](https://github.com/SidneyXu/JGSK)

[01]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/new_project.png
[02]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/create_maven_01.png
[03]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/create_maven_02.png
[04]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/create_maven_03.png
[06]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/install_plugins.png
[07]:   http://7xlqqp.com1.z0.glb.clouddn.com/jgsk/04/ident_folder.png




