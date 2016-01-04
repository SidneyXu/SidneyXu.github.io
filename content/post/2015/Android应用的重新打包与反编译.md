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

adb -e shell pm list packages | grep email

adb -e pull system/app/Email/Email.apk

mkdir email

tar -xvf email.zip -C email

keytool -printcert -file CERT.RSA

keytool -genkey -v -keystore androidse.keystore -alias androidsekey -keyalg RSA -keysize 2048 -validity 365

rm -rf email/META-INF

tar -zcvf email2.apk -C email .

jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore androidse.keystore email2.apk androidsekey


