---
comments: true
date: 2015-09-11T08:03:34+08:00
description: ""
draft: false
keywords:
- idea
- 快捷键
- template
tags:
- tools
title: Intellj IDEA 的使用
toc: true
topics:
- Dev
url: "post/usage-of-idea"
---

# Intellj IDEA 的使用

## 快捷键

|              快捷键 |        说明        | 
| ---------------: | :--------------: | 
|            cmd+n |       查找类        | 
|        control+n |       生成代码       | 
|        cmd+alt+l |       格式化        | 
|            cmd+f |       查找文本       | 
|            cmd+r |       查找替换       | 
|            cmd+p |      方法参数提示      | 
|        control+j |    quick doc     | 
|      cmd+shift+n |       查找文件       | 
|  cmd+alt+shift+n |     查找类中的成员      | 
|         shift+f6 |       重命名        | 
|            cmd+y |       删除行        | 
|            cmd+o |      重载父类方法      | 
|            cmd+e |      最近访问列表      | 
|            cmd+b |      跳转到声明       | 
|            cmd+] |     跳转到括号的结尾     | 
|      alt+shift+c |      对比最近代码      | 
|            cmd+w |    选择词，行，代码块     | 
|        control+h |     查看类的继承层次     | 
|        cmd+alt+t |  surround with   | 
|     ctrl+shift+t | 建立测试类或者在类与测试类中跳转 | 
| shift+cmd+delete |      上次编辑位置      | 
|        cmd+alt+m |     提取代码块为方法     | 
|        cmd+alt+p |     提取变量为参数      | 
|        cmd+alt+v |     提取为局部变量      | 
| control+option+h |      显示方法树       | 
|     fn+option+f7 |     显示变量的调用处     | 
|      cmd+shift+a |   显示action 窗口    | 

## 常用设置

### 在被修改的文件标签上显示星号

`Editor -> General -> Editor Tabs -> 勾选 Mark modified tabs with asterisk`

### 显示行号

`Editor -> General -> Appearance -> 勾选 Show line numbers`

### 自动导入引用的包

`Editor -> General -> Auto Import -> 勾选 Add unambiguous improts on the fly`

### 关闭重启后自动打开最后一个工程

`Appearance & Behavior -> System Settings -> 去掉 Reopen last project on startup 的勾`

### 设置编码格式

`Editor -> File Encodings -> IDE Encoding, Project Encoding, Default encoding for properties files 全部设置为 UTF-8`

### 修改自动完成快捷键

`Appearance & Behavior -> Keymap -> Main Menu -> Code -> Completion -> 修改 Basic 的值`

## 使用技巧

### 在命令行中通过 IDEA 打开指定文件

1. `Tools -> Create Command-line Launcher -> 在弹出的对话框中修改 Name 为 “idea"`
2. 在命令中执行 `idea 文件名` 就可以使用 IDEA 打开指定文件。

### 修改 toString() 实现方式

IDEA 通过 `ctrl+n` 快捷键可以自动生成 `toString()`方法，但是默认的实现方式是通过加号进行字符串连接，因此不适合字段特别多的类。要想提高效率，可以在生成时 Template 属性选择 `StringBuilder (JDK1.5)` 来使用 StringBuilder 替换。

### 修改默认 .gitignore

IDEA 或 Android Studio 在生成 Android 项目时会自动生成 `.gitignore` 文件，但是默认的文件通常都不符合需求，每次都手动改也非常麻烦，这时可以修改默认的 `.gitignore` 模板。

Mac 平台下 `.gitignore `模板位置为 `/Applications/Android Studio.app(或 IDEA.app)/Contents/plugins/android/lib/templates/gradle-projects/NewAndroidProject/root/project_ignore`

### 建立 Live Template

所谓的 Live Template 是一种代码模板，比如说在 IDEA 中输入 `fori`就可以生成标准的 for 循环语句，输入 `psfi` 就可以生成 `public static final int`，输入 `sout` 就可以生成输出语句。通过建立自己的 Live Template 可以减少重复劳动，加速开发。

以下以建立一个Android的Fragment模板来介绍如何建立 Live Template：

1. 选择 `Editor -> Live Templates`
   
   ![](images/2/template.png)
   
2. 点击右边的 `+` 号，选择 `Template Group`，输入 `group name` 为 "android"
   
3. 选择刚生成的 `android group`，继续点击 `+` 号，选择 `Live Template`，下方的 `Abbreviation` 表示快捷键，在这里输入 "nfr”，`Description` 输入 "create new fragment"
   
4. 在 `Template text` 输入以下文字，其中 "$" 开头的字符表示变量

``` java
private static final String $ARG_PARAM$ = "$CLASS_NAME$.$ARG_PARAM$";

private $ARG_CLASS_DITTO$ m$INST_VAR$;

public static $CLASS_NAME$ newInstance($ARG_CLASS$ $ARG_VAR$) {
    $CLASS_NAME$ fragment = new $CLASS_NAME$();
    Bundle args = new Bundle();
    args.put$ARG_CLASS$($ARG_PARAM$, $ARG_VAR$);
    fragment.setArguments(args);
    return fragment;
}

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    m$INST_VAR$ = getArguments().get$ARG_CLASS$($ARG_PARAM$);
}
```

1. 下方的 `No applicable contexts` 点击 `Define` 按钮，选择 `Java` - `Declaration`，这个选项表示在什么文件的什么位置允许使用该快捷方式，之所以只选择 "Declaration" 是因为我们需要建立的是方法的定义，所以这个快捷方式不应该出现在 "Comments"，"String" 或者其它方法内部。
2. 修改完毕后随意建立一个 Java 文件，输入 "nfr" 就可以直接生成上面模板定义的代码，并且光标自动定位到第一个变量处，修改变量名后按下回车就可以跳转到下一个变量处。

### 添加 File Template

Live Template 主要用于在文件中生成代码，而 File Template 则是用于根据模板生成文件。

以下以创建一个单例类来介绍详细步骤

1. 在工程的任意目录单击右键，选择 `New` - `Edit File Templates..`
   
2. 单击 `+`号，`Name` 输入 “Singleton”，`Extension` 输入 `java`
   
3. 内容处输入以下文本，点击 `OK`按钮
   
   ``` java
   #if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
   #parse("File Header.java")
   public class ${NAME}{
       private static ${NAME} ourInstance = new ${NAME}();
   
       public static ${NAME} getInstance() {
           return ourInstance;
       }
   
       private ${NAME}() {
       }
   }
   ```
   
4. 在工程任意目录单击右键，选择 `New` - `Java Class`，在弹出的对话框中 `Kind` 选择刚才建立的 “Singeleton” 就完成了所有操作