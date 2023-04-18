---
title: 我的阅读写作工具
date: 2023-03-21T21:35:00+08:00
tags: ["reading", "writing"]
series: ["reading-writing"]
image: "https://storage.guangzhengli.com/images/ladder-theme.png"
desc: "介绍我的阅读和写作工具。"
---

<!--more-->

本篇介绍的是我的写作和阅读工具的随笔，希望能对你有所帮助。

## 写作

写作最重要的事情就是拥有表达欲望，即自由的表达观点。如果一篇文章因为莫须有的理由而被删除，或者偷偷的被设置成仅自己可见，那么是极其容易消磨写作欲望的。笔者几乎从不发表专业学科以外的观点，但是仍然有过被封号的不愉快的经历。所以这也是笔者搭建个人博客的最重要的原因。

其次是如今其它写作平台的生命周期并不长，如果文章数据如果不控制在自己手上的话，笔者也很难相信商业公司的操守。

然后加上文字排版的追求，一个网页的设计从小清新变成狗皮膏药可能就在转瞬之间，更别提对字体和排版的要求。

所以种种原因，我还是走上了搭建个人网站的道路，目前这篇文章就托管在网站上。大体上做了以下一些工作：

- 域名：在 [Cloudflare](https://www.cloudflare.com/) 上购买的域名，一年大概 $9 左右。
- 存储：
  - 文章：通过 [GitHub](https://github.com/) 进行版本管理存储。
  - 图片：通过 [Cloudflare R2](https://developers.cloudflare.com/r2) 存储，每月免费的一千万读操作和一百万的写操作应该够我一直用了。
- 网站托管：[Cloudflare Pages](https://developers.cloudflare.com/pages/) 静态网页托管，免费。
- 样式主题：自己写的一个主题 [Hugo-Ladder](https://github.com/guangzhengli/hugo-theme-ladder)，放在 GitHub 上。如果你也想选择这个主题，可以参考我另外一篇文章 [如何 30 分钟搭建一套完整独立博客](https://guangzhengli.com/blog/zh/how-to-create-your-blog-for-free-by-hugo-ladder-in-30min/)。
- 字体选择：[霞鹜文楷](https://github.com/lxgw/LxgwWenKai)，我现在基本文字编辑器都选择这个字体。
- 评论系统：基于 [GitHub Discussions](https://docs.github.com/en/discussions) 的开源工具 [Giscus](https://giscus.app/)。
- 网站数据统计：
  - 自己搭建的一个 [umami dashboard](https://analytics.guangzhengli.com/share/o3zAba1V/guangzhengli)，免费托管在 [Vercel](https://vercel.app) 上，数据库用的是 [Supabase](https://app.supabase.com/) 500M 的免费额度。感兴趣可以参考我另外一篇文章 [如何零成本给博客集成 umami 数据统计分析功能](https://guangzhengli.com/blog/zh/how-to-integrate-umami-for-free-to-blog-site/)。
  - Google Search Console，有助于提升网站 SEO 排名。
  - Bing webmasters，同理提升网站 SEO 排名。

这就是这个网站用的所有工具了。基本上除一年 9 刀的域名费用外，无需使用其它的开销。写作流程基本上是在本地写好文章后，通过 `git push` 推送到 GitHub，接着等待 [Cloudflare Pages](https://developers.cloudflare.com/pages/) 自动触发 build 操作，大概 2 分钟左右就能看到最新文章。

### 本地

那么如何在本地写文章呢？这就不得不提到 [Markdown 语法](https://www.markdownguide.org/)，这是一个通过轻量级标记实现自动排版的语言。笔者现在已经很难接受除了 Markdown 以外的文字编辑器，诸如 Microsoft Word 或 Google Docs 之类的富文本编辑器。

不过 Markdown 也有一个缺点，就是不能所见即所得。所以就需要一个专业的 Markdown 写作编辑器来实现所见即所得，目前笔者用过最好的 Markdown 写作编辑器还是 [Typora](https://typora.io/)。虽然 [Typora](https://typora.io/) 已经开始收费了，但是好在是买断制，笔者也就欣然接受了。除此之外还有开源的 [妙言](https://miaoyan.app/) 也是不错的平替。

在购买 Typora 后由于对默认的排版样式不太满意，所以基于 [TailwindCSS](https://tailwindcss.com/) 和上面提到过的[霞鹜文楷](https://github.com/lxgw/LxgwWenKai) 又自己写了一个主题叫 [typora-ladder-theme](https://github.com/guangzhengli/typora-ladder-theme)，长下面这样。你也可以通过 [Typora 主题商店](https://theme.typora.io/theme/Ladder/) 下载。

![ladder-theme](https://storage.guangzhengli.com/images/ladder-theme.png)

默认我喜欢用暗色的主题，偶尔也会切换到白色。

![screenshotr_2023-3-21T21-50-5](https://storage.guangzhengli.com/images/screenshotr_2023-3-21T21-50-5.png)

在本地写作使用 Markdown 时还有一个问题，就是图片如果存放到网站的静态文件夹下，那么写作时需要一直手动标明图片的存放位置。体验不是很友好，所以笔者一般通过开源工具 [uPic](https://github.com/gee1k/uPic) 将图片先上传到 [Cloudflare R2](https://developers.cloudflare.com/r2) 上，流程一般是复制图片后按使用快捷键，得到 uPic 自动返回图片的 markdown 地址，粘贴在文章中即可。Typora 也默认附带了 uPic 的插件集成，也可以实现复制到 Typora 自动上传图片。

## 阅读

>  我承认我从写作中也获得了许多快乐，但是，这种快乐并不能代替读书的快乐。有时候我还觉得，写作侵占了我的读书的时间，使我蒙受了损失。写作毕竟是一种劳动和支出，而读书纯粹是享受和收入。

阅读时使用的工具主要分为读书和碎片信息两种。

### 书籍

> **更新：阅读书籍的收集工具可以参考我的另外一篇文章：[把阅读作为方法](https://guangzhengli.com/blog/zh/reading/)。**

电子书下载目前使用 Zlibrary，阅读设备目前主力是 MBP 和 ipad mini5，阅读软件使用自带的 Books。

### 碎片信息

有丰富的书籍，为什么还需要互联网上的碎片化信息呢？一是因为书籍的迭代周期很长，想要获取最新的知识和信息并不能全部指望书籍。二是书籍可以作为骨架，一些优质的碎片信息可以填充到骨架中，不断完善知识系统。

所以在互联网上寻找优质的碎片信息也是非常有必要的，但是对比寻找已经经过长时间检验的优质书籍，寻找优质的碎片信息往往非常困难，其获取的渠道往往也是迭代的最快的。个人将自己的获取碎片信息的渠道列出，仅作为参考：

-   Twitter：无论是技术界还是其它领域的消息，Twitter 往往能得到一手的最新信息，前提是你关注了相应领域的博主。就像如果你关注前端的技术，那么类似 Lee Robinson、Evan You 等人都是前端风向标般的存在，关注他们往往能获取行业最新进展。其它领域同理，根据自己的兴趣探索即可。
    
-   博客：通过 RSS 订阅相关领域的博客网站，偶尔能发现一些专业性强的好文和推荐。例如关注技术博客的话，诸如一些科技公司的博客：[Netflix Tech Blog](https://netflixtechblog.com/)，[AWS Architecture Blog](https://aws.amazon.com/cn/blogs/architecture/)，[Thoughtworks 洞见](https://insights.thoughtworks.cn/)，[美团技术团队](https://tech.meituan.com/)，[PingCAP 博客](https://cn.pingcap.com/blog/) 这些都是我见过维护内容的较好的博客网站。除此之外还有很多的独立技术博客，内容质量也非常高，不过这些内容因人兴趣而定，这里就不做过多推荐。
    
-   newsletter：其是一种定期发布包含有关特定主题的电子邮件，因为自由度高，隐私性好，在内容被随意监控和窃取的今天又重新焕发活力。像技术类的 [hackernewsletter](https://hackernewsletter.com/)，[ByteByteGo](https://blog.bytebytego.com/)，商业类的如 [appeconomyinsights](https://www.appeconomyinsights.com/) 都是个人非常喜欢的订阅。
    
-   网站、公众号等：除了上面几种渠道，还有一些日常访问的网站和公众号推送等，也能获取一些知识和最新资讯等，这类也属于因人而异型，每个人基于兴趣和爱好获取即可。
    

### 碎片信息收集

对比图书这种规范的、有体系的收集方式，想要收集和利用好碎片信息并不是一件容易的事情，例如你看到一个有价值的博客、Twitter Thread，一篇好的帖子、想法或者优秀的APP，想要将它收集起来，以便后续利用起来，但你会发现它们格式并不相同，它们本身没有关联，难以统一格式归纳。二是如果不能抓取完整的网站内容，也无法进行保存和搜索利用。

遗憾的是，关于这些缺陷目前并没有很好的解决办法，无论你是使用收藏网页，还是手动记录到备忘录中，都无法支持第二点。即使是使用像 [Readwise Reader](https://read.readwise.io/) 这类专业的一站式信息收集处理系统，都无法完全的解决第二点，依旧有大量网站内容无法完美抓取。目前我个人会使用不同的工具来缓解这一问题。

-   博客类的网站往往会有 RSS 源或者 newsletter 订阅，newsletter只需要用邮箱订阅即可，RSS 源我使用的是Chrome 插件 [RSSHub Reader](https://chrome.google.com/webstore/detail/rsshub-radar/kefjpfngnndepjbopdmoebkipbgkggaa) 来寻找，配合 [Netnewswire](https://netnewswire.com/) 来进行订阅和阅读。感兴趣试试下载插件后在本站能否复制 RSS 链接订阅。
    
-   使用 [cubox](https://cubox.pro/) 来进行一站式信息收集处理系统，负责收集不同类型的网站和资源。(2023--4-17更新，使用 [omivore](https://github.com/omnivore-app/omnivore) 替代)
    

笔者认为对于收集碎片信息这一流程，使用什么工具并不重要，无论是使用移动设备上的备忘录顺手记录，还是专业的工具收集，即然都无法做到完美，那么不如将重心放到定期回顾碎片信息和整理上。无论是将它们和后续的输出计划整理到一起，或者是阅读后填充到笔记中，完善整个知识体系才是最终目标。

## 总结

笔者认为，坚持写作是一件难事，首先需要自己拥有表达的欲望，才能驱动自己开始写作，而坚持写作，则靠的是热爱和反馈。

虽然笔者在本篇中推荐了很多工具，但是我自认并不是一个好的写作人。我有着持续性的阅读，抽风式的写作习惯，可能突然在某些时刻拥有强烈的表达欲望，于是开始捣鼓新工具来帮我写作，接着新鲜感过去后又开始长期摆烂。

时间一长笔者也开始怀疑自己到底是热爱写作，还是热爱折腾新工具。包括我其实还写过一些的纯粹的技术博客，只是觉得当时作为技术小白写的不够好，所以就没有收录进来。我很害怕时间一长又会有类似的情况发生。

但是仔细思索，无论未来怎样，如今的我有表达欲望，那么就应该开始写作，收获这种由写作带来的单纯快乐！

