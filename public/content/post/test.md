---
Date: 2015-08-29
description: "a description"
keywords:
- development
tags:
- go
- development
title: Post Sample
url: post/post-sample
topics:
- Development
- golang
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

## 测试标题

### A

#### A1

##### A1-1

#### A2

### B

#### B1

#### B2

### C

#### C1

#### C2

#### C3



