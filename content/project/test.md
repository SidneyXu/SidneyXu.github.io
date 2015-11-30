---
date: 2015-08-30
description: "a description"
keywords:
- development
tags:
- go
- development
title: Project Sample
topics:
- Development
- golang
draft: true
download_url: http://www.baidu.com
official_url: http://www.bookislife.com
project_description: a test project
project_name: hello world
project_url: http://git.bookislife.com
project_author: "Sidney"
release_date: 2015-09-10
version: "0.1"
---

## one

abcdef 

## ShortCodes

### Youku

{{% youku XMTMyNDQxMTg3Ng %}}

### Image

{{% img src="/404.jpg" alt="" class="half" %}}

{{% img src="/404.jpg" class="third right" %}}

图文混排式样

line 1

line 2

line 3

## 自定义 layouts

导入其它layout

```
{{ partial "header.html" . }}
```

网站语言

```
{{ .Site.LanguageCode }}
```

i18n

```
{{ $i18n := index .Site.Data.i18n .Site.LanguageCode }}
```






