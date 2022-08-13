---
title: 如何 30 分钟搭建一套完整独立博客
date: 2021-07-18 20:29:08
tags: ["hugo", "ladder主题", "教程", "GitHub Pages"]
series: ["how to deploy blog"]
featured: true
---

本文是如何搭建免费的独立博客系列的第二篇，本文包括使用本博客主题 `hugo-ladder`，如果搭建评论和统计功能，如果是打算后续持续使用该主题和 `hugo` 来搭建和运营博客，建议跟随本篇文章来搭建。

<!--more-->

## Hugo

### 技术选型

搭建独立博客的方式有很多，有 Service 服务器型的，好处可以存储和计算，无限制的开发功能，而缺点的话也很明显，首先你需要一笔不菲的服务器费用来支持带宽和并发，如果博客是面向全球的，还需要考虑多地部署来提升访问速度。

还有一种便是本网站采用的`静态站点生成`方案，其原理便是生成需要的静态网页，托管到  CDN 或者免费平台 [Netlify](https://netlify.com/)、[Heroku](https://www.heroku.com/)、[GoDaddy](https://www.godaddy.com/)、[DreamHost](https://www.dreamhost.com/)、[GitHub Pages](https://pages.github.com/)、[GitLab Pages](https://about.gitlab.com/features/pages/)、[Surge](https://surge.sh/)、[Firebase](https://firebase.google.com/docs/hosting/)、[Google Cloud Storage](https://cloud.google.com/storage/)、[Amazon S3](https://aws.amazon.com/s3/)、[Rackspace](https://www.rackspace.com/cloud/files)、[Azure](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website)和 [CloudFront](https://aws.amazon.com/cloudfront/) 上.

对于开发者来讲，托管到 [GitHub Pages](https://pages.github.com/) 是最熟悉的方案，我们本篇文章也是基于此方案展开的教程。不过 [GitHub Pages](https://pages.github.com/) 对于部分地区的访问速度来讲并不友好，可以考虑使用其它自带 CDN 的免费平台来部署，例如本站使用的 [Cloudflare Pages](https://pages.cloudflare.com/)。

在`静态站点生成`方案中，有几种常见的方案，有很流行的 [Hexo](https://hexo.io/), 本博客采用的 [Hugo](https://gohugo.io/)，还有比较新的技术 [Next.js](https://nextjs.org/)。

其中 [Hexo](https://hexo.io/) 的生态是最丰富和完善的，在 GitHub 中能找到最多的主题适配、最完善的第三方支持。但是考虑到我后续会自己开发定制化的主题和功能，去翻阅了下 [Hexo](https://hexo.io/) 的官方文档，结果 [Hexo](https://hexo.io/) 的文档不能说是事无巨细吧，只能说是等于没有。[Next.js](https://nextjs.org/) 是非常符合未来的一门技术，也是我比较心动的方案，但是后续发现  [Next.js](https://nextjs.org/) 在开发博客的生态上目前还不够完善。

所以在对比了几项技术的优缺点后，我最终采用了Hugo。[Hugo](https://gohugo.io/) 是基于 `golang` 编写的快速、现代的静态网站生成器。在 benchmark 测试中可以一秒渲染 5000 篇文章，也具备完善的生态。

### 安装 Hugo

安装 `Hugo` 可以按照官方的文档一步一步安装 [hugo install](https://gohugo.io/getting-started/installing)。也可以按照下面命令快速安装。

{{< tabgroup >}}

{{< tab name="MacOS / Linux" >}}

```shell
brew install hugo
```


{{< /tab >}}

{{< tab name="Window" >}}

```shell
choco install hugo -confirm
```

{{< /tab >}}

{{< /tabgroup >}}

Hugo 提供了一个 `new` 命令来创建一个新的网站:

```shell
hugo new site my_website
cd my_website
```

