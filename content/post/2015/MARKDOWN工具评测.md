---
comments: true
date: 2015-09-11T10:55:34+08:00
description: ""
draft: false
keywords:
- markdown
slug: "markdown-tools"
tags:
- tools
title: "MARKDOWN工具评测"
toc: true
topics:
- Dev
---


## Mou

![](http://25.io/mou/img/1@2x.png)

[下载地址](http://25.io/mou/)

仅支持 Mac

对于国内用户来说，Mou是Mac上最有名的Markdown工具，然而它的代码高亮功能并不好用，实时预览在文章较长时会产生难以忍受的卡顿效果，是被我最早使用并且很快抛弃的编辑器。

<!--more-->

## MacDown

![](http://d.pr/i/PTTC+)

[下载地址](http://macdown.uranusjr.com/)

仅支持Mac，开源

对Mou进行改良后的工具，比Mou要出色，支持快捷键操作，YAML *Front Matter* ，样式自定义，Toc（但是无法对每篇文章进行单独设置）。

## Ulysses

![](http://7xlqqp.com1.z0.glb.clouddn.com/write.png)

[下载链接](https://itunes.apple.com/cn/app/ulysses/id623795237?mt=12&ign-mpt=uo%3D4)

仅支持Mac，¥283.00

Mac上最有名的书写工具，体验很好，界面也很漂亮，支持文章的管理，但是使用的不是通用的Markdown语法，也不支持代码高亮，图片也只是显示为链接，适合专为书写的人，不适合码农。

## Byword

![](http://7xlqqp.com1.z0.glb.clouddn.com/byword.png)

[下载链接](https://itunes.apple.com/app/byword/id420212497?mt=12)

仅支持Mac，¥78.00

可以看做轻量级的Ulysses，没有代码高亮，也没有实时预览，虽然不适合码农，但是价格比Ulysses低不少。写完文章后也可以支持发布到 Wordpress，Blogger 和 Evernote 上，

## Write

![](http://7xlqqp.com1.z0.glb.clouddn.com/write.png)

[下载链接](http://writeapp.net/mac/download/)

仅支持Mac，¥68.00

不支持代码高亮，也没有实时预览，但是却支持文档管理，搜索功能和添加标签。默认创建的文件是 `txt` 格式，需要手动进入设置修改为 `md` 后缀，这也就说明了这款软件本质上在 markdown 功能上并不抢眼。还有一个亮点是其写完文章后也可以自动发布到 Wordpress 上，但是如果修改再次发布并不会修改原来的文章，而是作为新的一篇来发布。 Write 价格与上面两款相比更为低廉，纯写作的话可以考虑入手。

## Markdown Plus

![](http://7xlqqp.com1.z0.glb.clouddn.com/markdownplus.png)

[下载链接](https://tylingsoft.com/mdp)

支持Mac和Windows，¥30.00

国人开发的编辑器，支持Task List，数学公式，流程图，颜文字和Vim模式，功能非常强大，小缺点是不支持Toc，YAML *Front Matter* ，没有自动完成功能和快捷键，不支持自定义样式。比较奇特的一点是这款编辑器采用的是基于标题的同步滚动方式，而不是一般基于距离的滚动方式，虽然奇特但是写长文时更好用。可惜的是试了下在编辑长文时还是会产生难以忍受的卡顿。

## Texts

![](http://7xlqqp.com1.z0.glb.clouddn.com/texts.png)

[下载链接](http://www.texts.io/)

支持Mac和Windows

很简洁的编辑器，支持双平台，快捷键。亮点是编辑时编辑栏即是预览栏，输入内容后按下快捷键即可立即生成预览效果。此外，在编辑长文时完全不会产生卡顿也值得好评。但是缺点也很明显，要想显示正确格式，必须使用快捷键，而不能手动输入如 `#`,`-`等符号，这意味着从其它地方拷贝文本到Texts时这些文本也不会显示为Markdown，而是作为普通文本。此外Texts打开其它编辑器编辑的`.md`文本时会发现换行符的显示相当奇怪。

## Haroopad

![](http://7xlqqp.com1.z0.glb.clouddn.com/haroopad.png)

[下载链接](http://pad.haroopress.com/user.html)

支持Mac，Windows，Linux

由韩国人开发，支持多国语言，真正意义上的多平台编辑器，支持Linux这点特别好评。功能非常强大，支持Toc，Task List，数学公式，流程图，Vim模式，Markdown折叠，自动完成，自定义样式。最重要的是Haroopad渲染很快，编辑其它编辑器都无法胜任的长文时都不会产生卡顿。目前也是我使用时间最长的Markdown编辑器。当然其缺点也不少，在切换主题时有时会丢失高亮效果，在当初从0.13.0升级到0.13.1后我的Windows机器上的Haroopad界面从中文变为英文，使用部分特定主题会显示一片空白，而Mac机器上的Haroopad则丢失了自动完成功能。无论如何重装这些Bug还是依然存在于我的电脑上。尽管如此Haroopad还是非常优秀的，强烈推荐。

## Typora

![](http://7xlqqp.com1.z0.glb.clouddn.com/typora.png)

[下载链接](http://typora.io/)

目前为Beta版，仅支持Mac，官方有支持Windows和Linux的计划

一款极简主义的Markdown编辑器，支持快捷键，Task List，Toc，YAML Front Matters，颜文字，数学公式，代码高亮。最大的亮点是所见即所得，你输入的Markdown格式的文字会被立即转换为预览效果，不用费心在两栏中切换或者点击专门的切换按钮，可以让你专注于当前的文章中。在编写长文时没有Haroopad那样流畅，些许卡顿，但已经比其它很多Markdown编辑器要好得多。由于目前只是 Beta 版，还是存在一些这样那样的问题。

---

使用了那么多 Markdown 编辑器后，个人最推荐的还是 Write， Haroopad 和 Typora。

