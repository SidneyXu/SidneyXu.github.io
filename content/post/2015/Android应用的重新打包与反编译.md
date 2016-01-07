---
comments: true
date: 2016-01-04T17:31:18+08:00
description: ""
draft: false
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

<!--more-->

### 应用的重新打包

Android 开发中流传着所谓的“打包党”，即将其他开发者开发的畅销应用解开后修改部分内容（比如广告 Id 等）然后重新进行打包上传到商店以谋求暴利。以下就介绍以下如何对一个应用进行重新打包：

1. 首先任意准备一个应用用于进行测试，这里随意写了一个应用 `testapp.apk`

2. [下载 Apktool](http://ibotpeaches.github.io/Apktool/install/)，按照说明进行安装

3. 解开 `testapp.apk`

    ```bash
    ./apktool d testapp.apk -o testapp
    ```

4. 查看解开的目录下的 `META-INF/CERT.RSA`，该文件存储有原应用的签名

```bash
keytool -printcert -file testapp/original/META-INF/CERT.RSA
```

5. 模仿“打包党”，尝试修改 `AndroidManifest.xml` 中的包名和应用名。

6. 重新打包应用为 `new_testapp.apk`

    ```bash
    ./apktool b testapp -o new_testapp.apk
    ```

7. 生成一个新的 keystore

    ```bash
    keytool -genkey -v -keystore androidse.keystore -alias androidsekey -keyalg RSA -keysize 2048 -validity 365
    ```

8. 用刚刚生成的 keystore 对重新打包生成的 `new_testapp.apk` 进行重新签名

    ```bash
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore androidse.keystore new_testapp.apk androidsekey
    ```

9. 验证签名是否正确打到应用上

    ```bash
    jarsigner -verify -verbose -certs new_testapp.apk
    ```

10. 对齐 apk，应用要想上 Google Play 必须进行对齐操作，对齐后生成新的应用 `new_testapp_align.apk`

    ```bash
    $ANDROID_HOME/build-tools/23.0.2/zipalign -v 4 new_testapp.apk new_testapp_align.apk
    ```

以上就完成了最简单的重新打包操作。

### 反编译应用

1. 下载必要软件

    - [下载 dex2Jar](https://github.com/pxb1988/dex2jar)
    - [下载 j2-gui](http://jd.benow.ca/)

2. 修改权限

    ```bash
    chmod +x jd-gui-1.4.0.jar
    ```

3. 解压需要反编译的 apk 获得其中的 `classes.dex` 文件

4. 反编译 dex 文件为 class 文件

    ```bash
    dex2jar-2.0/d2j-dex2jar.sh testapp/build/apk/classes.dex
    ```

    执行完毕后会在当前文件夹下会生成 `classes-dex2jar.jar` 文件，该文件包含所有该应用相关的 class 文件

5. 打开 jd-gui，选择刚才生成的 jar 文件就可以看到反编译后获得的内容

    ```bash
    java -jar jd-gui-1.4.0.jar
    ```





