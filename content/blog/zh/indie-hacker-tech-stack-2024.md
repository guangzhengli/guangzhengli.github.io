---
title: 独立开发技术栈 2024
date: 2024-03-25T22:00:00+08:00
tags: ["indiehacker", "tech-stack", "cloud"]
series: ["share"]
featured: true
math: false
---

写这篇文章的目的纯粹是有了分享欲望，在技术栈和云服务厂商的选择如此丰富多样的今天，我们如果需要独立开发的话，应该需要选择什么技术栈呢？

<!--more-->

## 为什么是独立开发

为什么我要将技术的选型仅仅局限在独立开发上呢？因为如果我们不是独立开发，而是替公司或者客户开发的话，个人觉得技术选型的余地不大，乐趣也不高。

首先除了少有的创业公司，现有的公司或者项目一般都已经有了完善的技术栈和规范，更推荐的是在老的技术栈上进行重构，或者有计划的退休老的微服务，在新的微服务上引入新技术，而不是为了最求新颖的技术栈而全面推翻重来。

而就算在老的项目上引入新的技术栈，优先考虑的也是利于团队协同开发的技术栈，例如方便招人的、的技术。

所以如果想要满足自己的技术热情，或者追求 side project 的道路，最好的办法还是加入开源项目，或者开发自己的独立项目。

好啦，那我们正式开始推荐吧～

推荐指数的区别：

- 🌟🌟🌟🌟🌟：强烈推荐，非必要不替换。
- 🌟🌟🌟🌟：非常好用，但可以替换为其它。
- 🌟🌟🌟：能用，但考虑替换为其它。

## [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/)

如果你主要是构建前端应用，或者是前端 + 轻量级后端 API 的项目，那么目前最推荐的还是 [Nextjs](https://nextjs.org/) 这个框架，它是是一个基于 React 的开源框架。

推荐指数 🌟🌟🌟🌟🌟，推荐理由：

- 学习曲线平缓，并且官方的实战教程非常友好：[https://nextjs.org/learn](https://nextjs.org/learn)
- 生态非常的丰富，有非常多的可以满足不同需求的脚手架，例如 [create-t3-app](https://github.com/t3-oss/create-t3-app) 这个开源的脚手架项目，可以选择性的集成鉴权、ORM、UI 等功能和技术栈。
- 部署简单，毕竟是 [Vercel](https://vercel.com/) 的母公司开发的框架，可以非常丝滑的部署到 [Vercel](https://vercel.com/) 上。

为什么一上来就推荐 [Nextjs](https://nextjs.org/)？一是 [Nextjs](https://nextjs.org/) 本身就是一个可以快速创建和部署的框架，可以快速上线我们的项目；二是 [Nextjs](https://nextjs.org/) 对于我们后面集成推荐的技术栈和平台都非常的友好方便。

例如你想要部署一个静态网站，比如文档教程，那么你可以使用 [Nextjs](https://nextjs.org/) 的 Static Site Generation (SSG)，搭配上一些 CDN 托管服务，例如 [Cloudflare Pages](https://pages.cloudflare.com/) 或者 AWS S3 + Cloudfront，就可以免费 + 快速的部署一个静态网站。例如 [nextra](https://nextra.site/) 这个项目就是一个基于 [Nextjs + SSG](https://nextra.site/docs/guide/ssg) 的文档类型开源项目。

当然你也可以部署一些需要轻量级 API 的全栈项目，例如博客、AI Chatbot等，那么你可以使用 [Nextjs](https://nextjs.org/) 的 Server Side Rendering (SSR) 或者 API Routes，搭配上一些云服务商的云函数，例如 [Vercel](https://vercel.com/) 的 [Serverless Functions](https://vercel.com/docs/functions)，或者 [Cloudflare Worker](https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-nextjs-site/)，就可以快速的部署一个全栈项目。

例如 [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/) 的核心人物 [leerob](https://leerob.io/) 的博客就是一个很好的例子，他的个人博客就是基于 SSR + API Routes 做了一些例如评论、订阅等功能，然后部署在 [Vercel](https://vercel.com/) 上。

对于独立开发者来说，最重要的就是快速的上线我们的项目，而不是花费大量的时间在造轮子、部署和维护上，所以 [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/) 是一个非常好的选择。

## TailwindCSS + Shadcn/ui

在 UI 样式框架的选择上，有非常多的选择，这里不得不佩服前端开发者的生态之丰富，这在其它的语言生态下是难以想象的。

这里笔者只推荐个人用的还算是比较舒服的 [TailwindCSS](https://tailwindcss.com/) + [Shadcn/ui](https://ui.shadcn.com/)。

[TailwindCSS](https://tailwindcss.com/) 推荐指数 🌟🌟🌟🌟🌟，推荐理由：

- 对比原来的 CSS 的写法，[TailwindCSS](https://tailwindcss.com/) 方便了很多，内置语法糖。
- 高度可定制，代码规范一致。
- 可以很好地与现代前端工具（如 PostCSS、PurgeCSS 等）集成。

这里我把 [TailwindCSS](https://tailwindcss.com/) 列为强烈推荐的还有一个重要原因是，在 GPT、Github Copilot 这样的代码生成工具普及的今天，AI 能够很好的生成 [TailwindCSS](https://tailwindcss.com/) + JSX 的代码，是一种对 AI 友好的代码风格，所以感觉 [TailwindCSS](https://tailwindcss.com/) 会更加主流。

[Shadcn/ui](https://ui.shadcn.com/) 推荐指数 🌟🌟🌟🌟，推荐理由：

- 超高自由度的 UI 框架，可以很好的与 [TailwindCSS](https://tailwindcss.com/) 配合。
- 自身的样式设计已经非常的美观，还有大量的代码样例可以直接使用，例如 [blocks](https://ui.shadcn.com/blocks) 里面大量的样例代码。

shadcn/ui 与常见的 Ant Design 和 Chakra 等 Component library 不同，它是一个使用 Tailwind CSS 封装 [radix-ui](https://www.radix-ui.com/)（基于 headless ui 的无样式组件库） 的项目。这样的做法是一把双刃剑，优点是可以高度定制化、按需使用等等，但缺点是安装和更新不方便，未来的更新可能会受到 radix-ui 的影响。

推荐 shadcn/ui 还有一个原因也与 AI 有关，shadcn/ui 作者已经加入了 [Vercel](https://vercel.com/) 公司开发 [V0](https://v0.dev/) 这个 AI 前端代码生成工具，所以个人还是比较看好 shadcn/ui 的未来。

综合来看关于 shadcn/ui 大家可以按需选择，关于 shadcn/ui 的更多细节可以看[这篇文章](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/why-shadcn-ui-is-so-popular-in-2023-0f95e66f3ddc)。

另外还有一些常见的值得一试的 UI 框架还有：[daisyui](https://daisyui.com/) 和 [chakraui](https://chakra-ui.com/)。 

## References

- https://nextjs.org
- https://pages.cloudflare.com
- https://nextra.site
- https://nextra.site/docs/guide/ssg
- https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-nextjs-site/
- https://tailwindcss.com/
- https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/why-shadcn-ui-is-so-popular-in-2023-0f95e66f3ddc
- https://www.radix-ui.com/