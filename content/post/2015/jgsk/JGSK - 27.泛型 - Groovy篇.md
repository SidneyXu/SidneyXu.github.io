---
comments: true
date: 2015-10-04T21:26:27+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-27-generic-groovy"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 27.泛型 - Groovy篇
toc: true
topics:
- JGSK
---

Groovy 中使用的就是 Java 的泛型，所以参考 Java 就行了。但是要注意的是由于 Groovy 的动态特性，所以有些Java 会报的编译错误在 Groovy 中只有运行时才会发现。

如以下代码在 Java 中是非法的，在 Groovy 中只有运行时才会报错

```groovy
List<Date> dateList = new ArrayList<>()
dateList.add(1)
dateList.add(new Date())
```

---

项目源码见 [JGSK/_27_generics](https://github.com/SidneyXu/JGSK)



