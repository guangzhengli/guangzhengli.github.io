---
title: 如何 10 分钟快速搭建你自己的独立博客
date: 2021-07-18 20:29:08
tags: ["hugo", "ladder主题", "教程", "GitHub Pages"]
series: ["如何搭建独立博客系列"]
featured: true
---

本文是如何搭建独立博客系列的第一篇，也将教大家如何快速的使用 [GitHub Pages](https://pages.github.com/) 的功能来搭建属于自己的博客。

本篇文章属于快速入门版，可以帮助大家快速搭建和观察效果。如果是打算后续持续使用该主题和 `hugo` 来搭建和运营博客，建议跟随这篇文章来搭建 [如何 30 分钟搭建一套完整独立博客]({{< ref "blog/how-to-add-comments-and-analytics-to-blog-site-in-30min.zh" >}})。

<!--more-->

## 准备工作

### 创建 [GitHub](https://github.com/) 账号

在搭建自己的博客前，我们需要先注册一个 [GitHub](https://github.com/) 账号，这个账号的账户名非常重要，它是后面我们博客的域名地址。

例如我们创建的账号名称是 guangzhengli，那么我们的博客地址就是 https://guangzhengli.github.io。

### 创建 GitHub Pages 仓库

完整的教程可以查看 [官方教程](https://pages.github.com/)，简单来讲就是 [创建一个新的 repository](https://github.com/new)，名字为 *username*.github.io。

<img src="https://cdn.jsdelivr.net/gh/guangzhengli/PicURL@master/uPic/j0eHDj.png" alt="j0eHDj" style="zoom: 33%;" />

## 搭建博客

我们可以选择直接克隆已经准备好的仓库 [hugo-ladder-exampleSite](https://github.com/guangzhengli/hugo-ladder-exampleSite)

```
git clone https://github.com/guangzhengli/hugo-ladder-exampleSite.git
```

在拉取完代码后，我们需要修改配置信息，文件在根目录的 `config.yml` 文件中，我们需要将 Demo 的配置修改成自己。

> 请注意，一定记得修改配置信息，特别是拉取博客后大家会像评论配置等，否则将会给后续使用改教程的人带来困扰，谢谢🙏。如果想搭建自己独立的评论系统，可以参考这篇文章来搭建 [如何 30 分钟搭建一套完整独立博客]({{< ref "blog/how-to-add-comments-and-analytics-to-blog-site-in-30min.zh" >}})。

### 修改博客配置

根目录的 `config.yml` 文件，使用编辑器打开，修改如下大致的一些配置

```yml
baseURL: 'https://hugo-ladder.pages.dev' #修改为你的 https://username.github.io
homepage: 'https://hugo-ladder.pages.dev' #修改为你的 https://username.github.io
defaultContentLanguage: 'en' #修改默认语言，例如改为 zh 即默认使用中文
params:
  brand: HOME # 修改默认的为自己网站的标志
  avatarURL: /images/avatar.png #网站主页的照片信息，你可以在根目录 /static/images/ 里面替换成自己的照片
  author: Hugo Ladder # 修改你自己的名字
  authorDescription: # 修改对你自己的描述
  info:  # 修改对你自己网站描述
  favicon: /images/avatar.png #网站的 icon，你可以在根目录 /static/images/ 里面替换成自己的照片
  options:
    showDarkMode: true # 是否使用切换黑暗模式
    enableMultiLang: true # 是否展示多语言选择
 languages: # 如果你启用多语言，下面是中文展示
  zh:
    languageName: 中
    author: Ladder 主题
    authorDescription: 一个美观，快速并且专注于阅读的主题
    info: 帮助开发者构建一个免费并且漂亮的博客网站，记录自己的思考并且提高自己的影响力
```

### 注释评论相关功能

准备好的仓库 [hugo-ladder-exampleSite](https://github.com/guangzhengli/hugo-ladder-exampleSite) 主要给大家快速搭建使用。如果大家都使用同一套评论配置的话，会给后续使用该教程的人带来一定的困扰，可以先将相关功能注释掉。如果想搭建自己独立的评论系统，可以参考这篇文章来搭建 [如何 30 分钟搭建一套完整独立博客]({{< ref "blog/how-to-add-comments-and-analytics-to-blog-site-in-30min.zh" >}})。

修改方案如下所示

```yml
comments:
    giscus:
      enable: true
```

改为如下所示

```yml
comments:
    giscus:
      enable: false
```

## 如果添加和修改自己的博客文章

可以在根目录的 `content/blog` 中找到该站点的 Demo 展示文章，可以选择新增一个 `markdown` 文件添加自己想要表达的内容即可。

