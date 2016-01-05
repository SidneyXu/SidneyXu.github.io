---
comments: true
date: 2016-01-04T17:31:18+08:00
description: ""
draft: true
keywords:
- java
- android
- jd
- dex
slug: "android-repack-and-decompiler"
tags:
- android
title: "Android应用的重新打包与反编译"
toc: true
topics:
- Dev
---

## Android应用的重新打包与反编译

### 应用的重新打包

准备一个应用 `testapp.apk`

[下载 Apktool](http://ibotpeaches.github.io/Apktool/install/)，按照说明进行安装

解开 `testapp.apk`

```bash
./apktool d testapp.apk -o testapp
```

查看原应用中的包名

```bash
keytool -printcert -file testapp/original/META-INF/CERT.RSA
```

尝试修改 `AndroidManifest.xml` 中的包名和应用名。

重新打包应用

```bash
./apktool b testapp -o new_testapp.apk
```

生成一个新的 keystore

```bash
keytool -genkey -v -keystore androidse.keystore -alias androidsekey -keyalg RSA -keysize 2048 -validity 365
```

用刚刚生成的 keystore 对应用进行签名

```bash
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore androidse.keystore new_testapp.apk androidsekey
```

验证签名

```bash
jarsigner -verify -verbose -certs new_testapp.apk
```

对应 apk，应用要想上市场必须进行对齐操作

```bash
$ANDROID_HOME/build-tools/23.0.2/zipalign -v 4 new_testapp.apk new_testapp_align.apk
```

### 反编译应用

[下载 dex2Jar](https://github.com/pxb1988/dex2jar)
[下载 j2-gui](http://jd.benow.ca/)

修改权限

```bash
chmod +x jd-gui-1.4.0.jar
```

解压 apk 获得 dex 文件

反编译 dex 文件为 class 文件

```bash
dex2jar-2.0/d2j-dex2jar.sh testapp/build/apk/classes.dex
```

在当前文件夹下会生成 `classes-dex2jar.jar` 文件

打开 jd-gui，选择刚才生成的 jar 文件

```bash
java -jar jd-gui-1.4.0.jar
```





