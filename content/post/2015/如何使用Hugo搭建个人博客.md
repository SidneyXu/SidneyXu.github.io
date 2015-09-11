---
comments: true
date: 2015-09-11T15:49:56+08:00
description: ""
draft: false
keywords:
- blog
- markdown
- hugo
slug: "how-to-use-hugo"
tags:
- blog
title: 如何使用 Hugo 搭建个人博客
toc: true
topics:
- Dev
---


# 如何使用 Hugo 搭建个人博客

## 相关网站

- [官网](http://gohugo.io/)

- [Github](https://github.com/spf13/hugo)

- [主题](https://github.com/spf13/hugoThemes/)

推荐主题

- bootie-docs
- casper
- hyde-x

<!--more-->

## 使用 Hugo

### 安装

直接下载对应的安装包即可

[https://github.com/spf13/hugo/releases](https://github.com/spf13/hugo/releases)

如果是Mac平台的话也可以直接执行以下命令

```
brew install hugo
```

### 建立站点

```
hugo new site /path/to/site
```

如果是 Windows 平台的话则使用 "hugo.exe" 替代上述命令中的 "hugo"

```
hugo.exe new site /path/to/site
```

### 创建文章

进入刚创建的站点的根目录

```
cd /path/to/site
```

创建 about.md

```
hugo new about.md
```

编辑创建的 about.md 文件，在文件尾端添加以下内容

```
## A headline

Some Content
```

创建另一篇文章

```
hugo new post/first.md
```

创建后随意输入任意内容

### 下载主题

在根目录执行以下命令会下载所有主题

```
git clone --recursive https://github.com/spf13/hugoThemes themes
```

如果网速慢的话，上述方式很可能不成功，此时在站点根目录执行以下命令下载任一一个主题（此处下载的是 hyde-x 主题）

```
mkdir themes
cd themes
git clone https://github.com/zyro/hyde-x
```

### 运行服务器

```
hugo server --theme=hyde-x --buildDrafts --watch
```

以上，`theme` 用于指定主题名，`buildDrafts` 用于运行 build 草稿，`watch` 用于监控文件的改动。

启动完毕后可以在浏览器中输入以下命令观察生成的站点

```
http://localhost:1313
```

## 托管博客到 Github Pages

### Github Pages

Github Pages 是 Github提供的免费静态网页控件。总共分为两种：User 及 Project。

User 仓库名必须为 `username.github.io`，该仓库的 `master` 分支用于托管静态网站，访问地址为 `username.github.io`。

project 仓库名可以为任意，该仓库的 `gh-pages` 分支用于托管静态网站，访问地址为 `username.github.io/projectName`

### 提交静态网页到 Github Pages

Hugo 创建的静态网页全部都在站点根目录的 `public` 目录，所以只需要提交 public 下的所有文件到 Github 上就可以生成静态网站。所以最基本的方法就是建立两个分支，一个管理除 `public`文件夹之外的全部源文件，一个管理 `public` 文件夹下的所有文件。不过这种方式非常繁琐，我没有采用。

### 使用 Wercker 进行自动部署

Wercker 和 TravisCI 一样都是免费的 CI 服务。Wercker 建立在Docker上，可以帮我们自动完成静态网站的生成和提交。

#### 具体步骤

1. 访问[Wercker官方网站](http://wercker.com)
   
2. 注册账号
   
3. 选择 `settings` -> `Git Connection` -> 选择 `github` 后连接到你的github账号
   
   ![](/images/1/wercker-git-connections.png)
   
4. 点击 `create` 按钮，创建你的应用
   
   ![](/images/1/wercker-add-app.png)
   
5. 选择在 Github 上需要作为静态网站的仓库
   
   ![](/images/1/wercker-select-repository.png)
   
6. 选择仓库的拥有者，通常只要选择你自己的账号就可以了
   
   ![](/images/1/wercker-select-owner.png)
   
7. 配置访问权限 (Configure access)，直接选择默认的选择 [Next step]
   
8. 前面都执行后到这一步时 Wercker 会显示 `wercker.yml` 文件的代码模板，直接在作为静态网站的工程的根路径建立 `wercker.yml` 文件，拷贝网页上显示的代码到该文件并保存，选择 `Next Step`。
   
   ![](/images/1/werckeryml.png)
   
9. `Make my app public` 保持原样，选择 `Finish`
   
10. 此时项目已经初步设置完毕，画面上会显示 `Build now` 按钮，不过还没有到 build 的时间，接下来需要修改 `wercker.yml` 文件
    
11. 选择 `Registry`，检索 `hugo build`
    
    ![](/images/1/wercker-search.png)
    
12. 根据提示将代码粘帖到 `wercker.yml` 文件
    
    ``` yaml
    box: debian
    build:
        steps:
            - arjen/hugo-build@1.5.0
    ```
    
13. 提交 `wercker.yml` 文件，此时 wercker 应该会自动进行第一次构建
    
14. 接下来继续检索 `gh pages`，根据提示继续修改 `wercker.yml` 文件，其中 `domain` 换成你自己的域名，这个值会被写到CNAME文件中，`$GIT_TOKEN` 为定义的一个变量，它的值之后会进行设置，`basedir` 为需要同步到 `gh-pages` 分支的文件夹名
    
    ``` yaml
    box: debian
    build:
        steps:
            - arjen/hugo-build@1.5.0
    deploy:
        steps:
            - lukevivier/gh-pages@0.2.1:
                token: $GIT_TOKEN
                domain: git.bookislife.com
                basedir: public
    ```
    
15. 选择应用 -> `Settings` -> `Deploy targets` -> `Add deploy targets`
    
    ![](/images/1/adding-a-github-pages-step.png)
    
16. 按照下图进行配置，其中 `Deploy target name` 可以任意填写，`GIT_TOKEN` 变量对应的 text 需要填写你在 github 上建立的 token，具体可参照 [Github help](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)
    
    ![](/images/1/configure-the-deploy-step.png)
    
17. 选择 `OK` 完成所有设置并提交 `wercker.yml` 文件，此时 wercker 应该会自动进行b uild 后并将 `public` 文件夹下的文件同步到 `gh-pages` 分支下。



问题汇总

1. 有时 build 后会报 `unknown host: github.com`，此时无视即可，服务器偶尔的抽风，过个几分钟再试就可以了
   
2. 如果报 `wrecker.yml` 无法解析的话通常是yml文件中含有tab键，全部替换成空格就可以了
   
3. 如果 build 成功完成了，但是deploy时报 `git: command not found`，这是应为没有安装 git，需要修改 `wrecker.yml` 来安装git，完整代码如下
   
   ``` yaml
   box: debian
   build:
       steps:
           - arjen/hugo-build@1.5.0
   deploy:
       steps:   
           - script:
               name: install git
               code: |
                   apt-get update
                   apt-get install git -y
           - lukevivier/gh-pages@0.2.1:
               token: $GIT_TOKEN
               domain: git.bookislife.com
               basedir: public  
   ```
   
   
## 参考资料

- [Hugo Quickstart Guide](http://gohugo.io/overview/quickstart/)

- [Automated deployments with Wercker](http://gohugo.io/tutorials/automated-deployments/)

