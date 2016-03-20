---
comments: true
date: 2016-03-19T13:22:59+08:00
description: ""
draft: true
keywords:
- java
- groovy
- scala
- kotlin
slug: ""
tags:
- java
- groovy
- scala
- kotlin
title: 常用 adb 命令
toc: true
topics:
- Dev
---

## 常用 adb 命令

### 基本操作

列出当前所有设备名，包含真机和模拟器

```bash
adb devices
```

开启 adb 调试桥

```bash
start-server
```

关闭 adb 调试桥，当 Logcat 显示设备 offline 时可用通过重启调试桥恢复功能

```bash
kill-server
```

显示手机硬件信息，包括 device model 等

```bash
adb shell cat system/build.prop
```

### 应用相关

安装应用

```bash
adb install <apk>
```

卸载应用

```bash
uninstall <packageName>
```

显示所有安装的应用

```bash
adb shell pm list packages
```

打开 Schema

```bash
adb shell am start -W -a android.intent.action.VIEW -d <schema> <packageName>
```

- `-a` 指定 action
- `-d` 指定 data uri

打开 Activity

```bash
adb shell am start -n <packageName>/<targetActivity> [-e somekey somevalue]
```

- `-e` 相当于向 intent 放入 String 类型的 extra 参数，`-ei` 则为 int 类型 value，`-el` 为 long 类型 value，其它类推

>注意：如果该 Activity 不是 Main Activity 的话，则必须设置 `android:export=“true”` ，否则会报没有权限


### 文件操作

拷贝文件到手机

```bash
adb push <local_path> <remote_path>
```

从手机拷贝文件

```bash
adb pull <remote_path> <local_path>
```

### 截图

```bash
adb shell screencap -p <filename>
```

- `-p` 表示保存为 png 格式，不指定的话 filename 必须指定后缀名。

### wakelock

显示所有 wakelock 情况

```bash
adb shell cat /sys/kernel/debug/wakeup_sources
```

显示电源状况

```bash
adb shell dumpsys power
```

查找一个应用是否错误地占用了 wakelock

1. 按下电源键关闭屏幕
2. 等待 20s
3. 执行 `adb shell dumpsys power | grep PARTIAL`，查看输出结果是否有这样的形式 `PARTIAL_WAKE_LOCK ‘AudioOut_2’ activated(minState=0, uid=1013, pid=157)`
4. 重复 2-4 多次，如果还是一直能获得该锁则证明其存在 Bug，没有正常释放锁


