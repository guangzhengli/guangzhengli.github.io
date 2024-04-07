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

首先除了少有的创业公司，现有的公司或者项目一般都已经有了完善的技术栈和规范，更推荐的是在老的技术栈上进行重构，或者有计划的淘汰老的微服务，在新的微服务上引入新技术，而不是为了最求新颖的技术栈而全面推翻重来。

而就算在老的项目上引入新的技术栈，优先考虑的也是利于团队协同开发的技术栈，例如方便招人的、利于团队合作的技术。

所以如果想要满足自己的技术热情，最好的办法还是加入开源项目，或者开发自己的独立项目。

好啦，那我们正式开始推荐吧～

## Frontend

### [Nextjs](https://nextjs.org/)

如果你主要是构建前端应用，或者是前端 + 轻量级后端 API 的项目，那么目前最推荐的还是 [Nextjs](https://nextjs.org/) 这个框架，它是是一个基于 React 的开源框架。

推荐理由：

- 学习曲线平缓，并且官方的实战教程非常友好：[https://nextjs.org/learn](https://nextjs.org/learn)
- 生态非常的丰富，有非常多的可以满足不同需求的脚手架，例如 [create-t3-app](https://github.com/t3-oss/create-t3-app) 这个开源的脚手架项目，可以选择性的集成鉴权、ORM、UI 等功能和技术栈。
- 部署简单，毕竟是 [Vercel](https://vercel.com/) 的母公司开发的框架，可以非常丝滑的部署到 [Vercel](https://vercel.com/) 上。

为什么一上来就推荐 [Nextjs](https://nextjs.org/)？一是 [Nextjs](https://nextjs.org/) 本身就是一个可以快速创建和部署的框架，可以快速上线我们的项目；二是 [Nextjs](https://nextjs.org/) 对于我们后面集成推荐的技术栈和平台都非常的友好方便。

例如你想要部署一个静态网站，比如文档教程，那么你可以使用 [Nextjs](https://nextjs.org/) 的 Static Site Generation (SSG)，搭配上一些 CDN 托管服务，这里非常推荐 [Cloudflare Pages](https://pages.cloudflare.com/)，Cloudflare 是笔者唯一五星推荐的云服务商，后面还会再提到。例如 [nextra](https://nextra.site/) 这个项目就是一个基于 [Nextjs + SSG](https://nextra.site/docs/guide/ssg) 的文档类型开源项目。

当然你也可以部署一些需要轻量级 API 的全栈项目，例如博客、AI Chatbot等，你可以使用 [Nextjs](https://nextjs.org/) 的 Server Side Rendering (SSR) 和 API Routes，搭配上一些云服务商的云函数，例如 [Vercel](https://vercel.com/) 的 [Serverless Functions](https://vercel.com/docs/functions)，或者 [Cloudflare Worker](https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-nextjs-site/)，就可以快速的部署一个全栈项目。

例如 [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/) 的核心人物 [leerob](https://leerob.io/) 的博客就是一个很好的例子，他的个人博客就是基于 SSR + API Routes 做了一些例如评论、订阅等功能，然后部署在 [Vercel](https://vercel.com/) 上。

对于独立开发者来说，最重要的就是快速的上线我们的项目，而不是花费大量的时间在造轮子、部署和维护上，所以 [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/) 是一个非常好的选择，即使后面 Vercel 平台涨价或者不免费了，也可以替换为 cloudflare 等其它云服务厂商。

### TailwindCSS + Shadcn/ui

在 UI 样式框架的选择上，有非常多的选择，这里不得不佩服前端开发者的生态之丰富，这在其它的语言生态下是难以想象的。

这里笔者只推荐个人用的还算是比较舒服的 [TailwindCSS](https://tailwindcss.com/) + [Shadcn/ui](https://ui.shadcn.com/)。

[TailwindCSS](https://tailwindcss.com/) 推荐理由：

- 对比原来的 CSS 的写法，[TailwindCSS](https://tailwindcss.com/) 方便了很多，内置语法糖。
- 高度可定制，代码规范一致。
- 可以很好地与现代前端工具（如 PostCSS、PurgeCSS 等）集成。

这里我把 [TailwindCSS](https://tailwindcss.com/) 列为推荐的还有一个重要原因是，在 GPT、Github Copilot 这样的代码生成工具普及的今天，AI 能够很好的生成 [TailwindCSS](https://tailwindcss.com/) + JSX 的代码，是一种对 AI 友好的代码风格。

[Shadcn/ui](https://ui.shadcn.com/) 推荐理由：

- 超高自由度的 UI 框架，可以很好的与 [TailwindCSS](https://tailwindcss.com/) 配合。
- 自身的样式设计已经非常的美观，还有大量的代码样例可以直接使用，例如 [blocks](https://ui.shadcn.com/blocks) 里面大量的样例代码。

shadcn/ui 与常见的 Ant Design 和 Chakra 等 Component library 不同，它是一个使用 Tailwind CSS 封装 [radix-ui](https://www.radix-ui.com/)（基于 headless ui 的无样式组件库） 的项目。这样的做法是一把双刃剑，优点是可以高度定制化、按需使用等等，但缺点是安装和更新不方便，未来的更新可能会受到 radix-ui 的影响。

推荐 shadcn/ui 还有一个原因也与 AI 有关，shadcn/ui 作者已经加入了 [Vercel](https://vercel.com/) 公司开发 [V0](https://v0.dev/) 这个 AI 前端代码生成工具，所以个人还是比较看好 shadcn/ui 的未来。

综合来看关于 shadcn/ui 大家可以按需选择，关于 shadcn/ui 的更多细节可以看[这篇文章](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/why-shadcn-ui-is-so-popular-in-2023-0f95e66f3ddc)。

另外还有一些常见的值得一试的 UI 框架还有：[daisyui](https://daisyui.com/) 和 [chakraui](https://chakra-ui.com/)。 

### 其它

- [Prisma](https://www.prisma.io/): 数据库 ORM，即使性能不是最好的，但确实是生态是最好的，各类数据库和云厂商都有支持。方便后期数据量大后需要切换数据库。
  - [Drizzle](https://orm.drizzle.team/): 如果选择 Serverless 方案，可以考虑使用 Drizzle ORM，Drizzle ORM 针对 Serverless 场景做了专门的优化，打包后仅仅 31 KB。
- [NextAuth.js](https://next-auth.js.org/): Nextjs 的鉴权库，虽然文档是混乱了点，但生态和功能性确实是最好的。
- [SWR](https://swr.vercel.app/): Nextjs 团队出品，HTTP 请求库，解决组件数据请求和缓存的问题。
- [zod](https://zod.dev/): Schema 验证库，可以用于前后端数据和表单的校验。
- [Zustand](https://zustand.surge.sh/) & [jotai](https://jotai.org/): Zustand 是一个简单的 React 状态管理库，可以替代 Redux。jotai 是一个基于原子状态的状态管理库，具体可以根据需要选择。
  - 关于状态库的选择，相关视频非常推荐这个：[React Query Essentials](https://www.youtube.com/watch?v=5-1LM2NySR0)。关于什么是状态库管理文章推荐这个系列：[practical-react-query](https://tkdodo.eu/blog/practical-react-query)。
- [driverjs](https://driverjs.com/): 引导用户操作的库，可以用于新手引导、操作引导等。
- [wxt](https://github.com/wxt-dev/wxt): 一个类似 Nuxt 的浏览器插件开发框架，可以用于开发 Chrome 、Firefox 等插件。
- [orama](https://oramasearch.com): 前端全文搜索替代 algolia 的方案，nodejs 新官网就是使用的这个搜索引擎。

## Backend

说完了前端库和平台的选择，我们来到后端的领域，其实纯后端的选择反而不多。因为后端想要提供服务，离不开 CPU、内存、带宽等资源，而这些资源各大花销都不少，所以我更多推荐 Serverless 的方案。如果你想要熟悉的后端框架，也可以使用 [Railway](https://railway.app/)、[Fly.io](https://fly.io/) 等平台来部署容器。

### Cloudflare Worker

选择 Serverless 是最省钱的方案，因为一切都是按量付费，这意味着在你的独立项目开始一段时间内，不用担心服务费用的问题，唯一要小心的就是云服务平台打着免费的幌子，等你深度使用后再收割韭菜，毕竟 Serverless 方案换云服务平台还是比较麻烦的。所以如果你想要选择 Serverless 方案，我唯一推荐的就是 [Cloudflare Worker](https://workers.cloudflare.com/)。

[Free Plan](https://developers.cloudflare.com/workers/platform/pricing/#workers) 就可以提供 100,000 次/天的请求，这能应付大部分的项目，唯一不足的是 Free Plan 的 10ms 的 CPU 执行时间非常短，会导致很多的计算超时失效。但是好在 $5/月的 [Standard Plan](https://developers.cloudflare.com/workers/platform/pricing/#workers) 并不贵，提高到 30s 的 CPU 执行时间和超多的时间额度，这对于大部分的项目来说已经足够了。

并且 [Cloudflare Worker](https://workers.cloudflare.com/) 会和我们后面推荐的其它 Cloudflare 服务很好的结合，例如 KV(键值数据库)、Pages(静态页面)、R2(对象存储)、D1(关系型数据库)、Queue(MQ 队列) 等，这些服务大部分都是可以免费使用和共享 Standard Plan 额度。

选择 [Cloudflare Worker](https://workers.cloudflare.com/) 后，我们既可以使用原生的 nodejs，因为它的 Worker 代码是基于 V8 引擎的，所以你可以使用 JavaScript、TypeScript、Rust 等语言来编写你的 Worker 代码。

也可以使用像 [hono.dev](https://hono.dev/) 这样的 Serverless 框架，它是面向 edge runtime 的 web 框架，提供了很多的开箱即用的功能，例如路由、中间件等。它还提供不同 Serverless 平台的兼容性，例如 [Vercel](https://vercel.com/)、[AWS Lambda](https://aws.amazon.com/lambda/) 等。

### Railway & Fly.io

如果你的项目不适合使用 Serverless 方案，毕竟 Serverless 还是存在一些缺陷，例如 CPU 时间、易用性、调试等问题，那么你可以选择你已经熟悉的后端语言，选择容器部署的方案。

关于后端的语言和框架，这里不做推荐，因为后端语言和框架的选择更多是取决于你的熟悉程度，笔者因为工作和开源，用过 Java/Spring、C#/.NET、Ruby/Rails、Nodejs/Express、Python/FastAPI、Go/Gin 等后端框架，每个框架都有自己的优缺点，这里我只推荐你使用熟悉程度高和开源生态好的框架，因为在部署费用差不多的情况下，选择熟悉的框架可以提高开发效率。

推荐使用容器部署的方案，而不是自己管理主机部署，关于容器部署的平台，推荐 [Railway](https://railway.app/) 和 [Fly.io](https://fly.io/)，这两个平台都是容器部署和管理比较丝滑的平台，除了部署后端外，也可以部署数据库等服务。推荐理由是这两个平台因为个人开源活跃度还算高的原因，都送了我每月 $5 的免费额度，而且用完后确实感觉还不错。

## Auth

关于 Auth 相关的框架和云服务，我个人的体验是，如果你的业务 auth 需求不复杂，加上使用 Nextjs + [NextAuth.js](https://next-auth.js.org/) 这样的技术栈，那么直接使用 [NextAuth.js](https://next-auth.js.org/) 集成像 Google OAuth、Github OAuth、Azure AD 等社交登录和 JWT 鉴权是最方便的。

无需管理用户密码、无需自己负责登陆安全，只需要简单的配置和代码即可实现。更重要的是，不需要集成其它的云服务，减少了复杂度和成本。

像老牌的 [Auth0](https://auth0.com/) 或者新兴的 [Clerk](https://clerk.com/) 这样的云服务，虽然功能性和生态性都挺好，但是免费套餐比较少，后续的收费也非常的昂贵，不太推荐使用。

### [Supabase Auth](https://supabase.com/auth)

如果你的业务 auth 需求比较复杂，例如需要自定义登录、注册、密码找回、2FA认证等，那么推荐使用 [Supabase Auth](https://supabase.com/auth)。支持 50000 的月活用户，服务稳定，用户管理可以和数据库可以放在一起，而且支持社交登录、邮箱密码登录、2FA 等功能。

### [Cloudflare Zero Trust](https://developers.cloudflare.com/cloudflare-one/)

如果你的用户体量非常少(少于 50 人)，或者你需要给你的合作伙伴、家人、朋友提供一个安全的访问你的项目的方式，那么非常推荐使用 [Cloudflare Zero Trust](https://www.cloudflare.com/zh-cn/teams/zero-trust/)。Zero Trust 是一个基于 Cloudflare 的安全服务，可以通过不修改一行代码，仅仅是通过简单的配置，就可以让你的项目只能被你指定的用户邮箱或者固定IP访问。

这个方案的缺点是你的域名必须是通过 Cloudflare 托管，并且因为 Cloudflare Zero Trust 的免费额度是 50 人，超过之后非常的昂贵。所以这个方案只适合小团队或者个人使用。

但是优点也非常明显，不需要额外的代码和服务，只需要简单的配置，就可以让你的项目安全的被访问。非常适合私有的项目，例如搭建 [Lobe Chat](https://github.com/lobehub/lobe-chat) 或者 [ChatGPT-Next-Web](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web) 这样的 GPT 项目给家人朋友使用。甚至可以配合 Cloudflare R2 来搭建私有图床等，这里就不过多展开了。

## Database

数据库的选择非常的重要也非常的多样，因为本身数据库的选型就很多(MySQL、PostgreSQL、MongoDB、SQLite、Redis 等)，再加上云服务商的竞争，所以这里只推荐一些比较好用的云服务商。

### Supabase Database

如果是传统的关系型数据库，我推荐使用 PostgreSQL，PostgreSQL 的开源社区确实是一直以来最好的，体现在功能、易用性、开源生态上都胜过 MySQL 不止一筹。

对应的云服务厂商也有 [Supabase](https://supabase.com/) 这个备受好评的数据库提供商，它支持免费的 500M 存储的两个数据库项目，支持 Postgres 的所有功能，还有很多的插件和工具，例如 Vector [向量数据库](https://guangzhengli.com/blog/zh/vector-database/)等。虽然后续的收费也不便宜，但是对于独立开发者来说，免费和标准版足够在前中期使用了。如果后续不够用，可以考虑在 Fly.io 上自建数据库再迁移。

并且 Supabase 这个团队非常的活跃，开源社区也非常的好，除了我们上面提到的 [Supabase Auth](https://supabase.com/auth)，还有像[Supabase Storage](https://supabase.com/storage)、[Supabase Realtime](https://supabase.com/realtime)、[Supabase Functions](https://supabase.com/functions) 等服务，都是不错的服务。

与 Supabase Database 类似的还有 Serverless Postgres 如 [Neon](https://neon.tech/) 也还不错。

### Cloudflare D1

如果要选择更便宜并且能支撑项目稳定运行的数据库，我推荐使用 [Cloudflare D1](https://www.cloudflare.com/zh-cn/products/distributed-sql-database/)，它是一个 edge sqlite 数据库，虽然 SQLite 不如 Postgres 功能强大，但胜在可以部署在全球各地的 CDN 节点上，这意味着你的数据库可以离用户更近，减少延迟，提高速度。

而且最近看到 D1 和[Prisma](https://www.prisma.io/) 可以集成，这意味着你可以使用 Prisma ORM 来操作 D1 数据库，这对于全栈项目来说也是非常的方便。详情可以看这篇[博客](https://blog.cloudflare.com/prisma-orm-and-d1)。

选择 D1 的最大优势就是价格足够便宜，免费额度是 5GB，后续也只需要 $0.75/mo，并且同时可以使用Cloudflare KV、R2、Queues 等服务，可以很好的和 Cloudflare Worker 配合使用。

### Upstash

如果你的项目需要轻量级的使用 Redis，那么推荐使用 [Upstash](https://upstash.com/)，可以用来存储一些热数据、缓存等。这个云服务商同样提供像 Kafka、Vector 向量数据库等服务。并且后续也是按量收费，也算是比较推荐的。

## Payment

支付提供服务商的选择不多，因为支付关系到安全、税务、金融监管、法律等等，是最不推荐自己造轮子或者使用小众云服务商的。这里只推荐比较大且适合的。

### Stripe

[Stripe](https://stripe.com/) 是国外公认最好的支付服务商，支付全球大部分国家的货币，并且抽成也还算公道。从开发角度来讲，也是开发文档齐全，开源生态最完善的支付服务提供商。

关于 [Stripe](https://stripe.com/) 并不想花费太多笔墨，它的优点很明显，缺点也很明显，因为国内大部分的人都很难注册并使用 Stripe，注册 Stripe 除了一些偏门和有被封风险的方案，最稳妥的还是在国外注册一个公司，然后通过公司注册 Stripe。

但是对于独立开发者来讲，在国外运营一家公司并不是一件容易的事情，注册公司通过一些渠道还是比较容易的，但是公司的税务、法务等问题就比较复杂了，所以这里只是提一下，如果你有条件注册 Stripe，那么推荐使用 Stripe。

### [Lemonsquzz](https://www.lemonsqueezy.com/)

如果你没有条件使用 Stripe 的话，这里推荐使用 [Lemonsquzz](https://www.lemonsqueezy.com/) 作为替代，它也是一个国外的支付服务商，支持外国信用卡、PayPal 等支付渠道，也支持国内支付宝、微信等支付方式。特别在国外比较常见的订阅收款上，[Lemonsquzz](https://www.lemonsqueezy.com/) 支持支付宝进行订阅周期付款，可以做到国内国外用户的支付统一。

虽然它的抽成比 Stripe 要高，稳定性也不如 Stripe，但是国内的独立开发者可以直接注册使用，无需注册公司。并且也有一系列的增值服务可以使用，例如折扣管理、订阅管理、提供支付页面等等。所以如果你打算初期先跑通商业模式，那么可以考虑初期先用 [Lemonsquzz](https://www.lemonsqueezy.com/)，后期再考虑注册公司使用 Stripe。

## Statistics

对于数据观测来讲，一般有业务上的数据分析，例如访问量、哪个页面点击多、哪个功能受欢迎等等。也有技术上的数据分析，例如网站的性能、日志报错分析等。这里推荐几个比较好用的数据分析工具。

### [Umami](https://umami.is/) / [clarity](https://clarity.microsoft.com/)

对于网站的分析，我推荐使用 [Umami](https://umami.is/)，它是一个开源的网站分析工具，可以免费托管在 Vercel + Supabase 上，通过引入 script 来自己进行数据收集，不需要担心数据泄露和隐私问题。

目前我这个博客网站的数据分析就是使用的 [Umami](https://umami.is/)，大家可以点击这个[网站](https://analytics.guangzhengli.com/share/o3zAba1V/guangzhengli)来看看效果。

如果你想要更加强大的数据分析，例如用户行为录制、网站热点功能分析等，那么推荐使用微软出品的 [clarity](https://clarity.microsoft.com/)，它是一个宣称永久免费的网站分析工具，微软会收集数据投喂 AI 等等，但不会共享给第三方。

同理 Google Analytics 等也是不错的选择，有时间可以考虑都集成一下，毕竟有了数据，才能从里面挖掘出一些有用的信息。

### 分析工具

- Logs: 日志分析
  - [NewRelic](https://newrelic.com/) 提供 100G 的使用额度，可以用来记录 Log、APM 等，分析错误日志，API 性能等。
  - [betterstack](https://betterstack.com/logs/pricing) 提供 1GB/mo，经常用来集成纯前端项目的日志分析。
  - [sumologic](https://www.sumologic.com/pricing/) 最近发现的日志平台，用起来也还不错。
- Performance: 性能分析
  - [PageSpeed](https://pagespeed.web.dev/) Google 家的 PageSpeed Insights，可以用来分析前端网页性能。
  - [LightHouse](https://developer.chrome.com/docs/lighthouse/overview/) Google 出品的提高网页质量工具，包括但不限于分析网页性能、SEO、可访问性等。
- SEO: 搜索引擎优化
  - [Google Search Console](https://search.google.com/search-console/about) Google 官方的搜索引擎优化工具，可以用来分析网站在 Google 搜索引擎的表现。
  - [META SEO inspector](https://chromewebstore.google.com/detail/meta-seo-inspector/ibkclpciafdglkjkcibmohobjkcfkaef) Chrome 插件，可以用来分析网页的 SEO 信息。SEO 可能国内的开发者了解的不多，但是在一些国外很多的业务场景下，SEO 的重要性影响到项目的成功与否。

## Others

### IAC (Infrastructure as Code)

对于独立开发者来讲，经常要使用很多不同的云服务商，例如发送邮件需要使用 AWS 使用 SES，Azure 使用一些 Identity 的功能，Clouflare 需要一些服务，NewRelic 需要创建对应的日志集，这些云服务商都有自己的 API 和 CLI 工具，但是如果你要在多个云服务商之间切换，对于心智来讲是一个不小的负担。

所以推荐使用 IAC 工具来统一管理，这里推荐使用[Pulumi](https://pulumi.com/)，[Pulumi](https://pulumi.com/) 支持使用代码的方式来管理你的云服务商，推荐的理由主要是它支持主流云服务商，支持多种语言(JS, TS, Python等)，并且有很多的社区支持。

### 其它开发工具（暂时记起这些）

- [Buildkite](https://buildkite.com/): Pipeline 的额外补充，如果你选择的云服务商不提供 CI/CD 服务，或者无法集成 Github Actions，那么可以考虑使用 [Buildkite](https://buildkite.com/)。
- [Bruno](https://www.usebruno.com/): 一个类似 Postman 的本地 HTTP 请求工具，可以用来调试 API 请求。
- [proxyman](https://proxyman.io/) 代理抓包工具，可以用来调试、正向代理、反向代理等。


## 后记

最近在看《小而美：持续盈利的经营法则》这本书时，觉得作者提出了几个很有意思的观点，书中作者认为如果要做独立开发者，独立经营一个产品的话，最应该关注的，应该是尽快交付、尽早收费，不到最后不花钱。

首先尽快交付指的是快速迭代产品、快速试错。创建产品是在快速的反馈循环和迭代中汲取经验的过程，如果产品能够早早的面向消费者，那么消费者的沟通和反馈能够及时的帮助个人开发者，避免出现闭门造车的困境。对比起用一年的时间构建一个初始版本，然后每个季度发布一个大更新。也许将产品的构建时间缩短到月，然后每周甚至每天发布小版本的交付方式，更能够帮助独立开发者减少试错成本，尽快找到盈利的道路。

其次尽早收费也是非常重要的一环，即使你的产品收费 1 块钱，也和免费有本质的区别。独立开发者可以提供从免费开始的阶梯价格来进行产品定价，但绝不应该是完全免费，免费会拖垮产品的经营成本，也会让你无法判断产品的实际价值。正如经济学家丹·艾瑞里(Dan Ariely)在《怪诞行为学》(PredictablyIrrational)一书中所写的：“人们会对免费的东西趋之若鹜，即使这并不是他们想要的。”

一旦产品开始进行收费，消费者便会认真思考这个产品是不是给他们带来了实际的价值，并且解决了他们的实际问题。收费和定价是一个很好的评估标准，来帮助独立开发者判断开发出来的产品是不是有实际价值的，否则，开发者应该尽早思考是否应该将精力放到其它产品上去，放到其它更能带来实际价值的产品中去。

最后一个建议是不到最后不花钱，这也是我们本文的主题。虽然书中指的是独立开发者不要通过广告和烧钱来期望获得巨大增长，但笔者认为，减少成本是独立开发者必须认真思考的问题，否则，过高的成本会带来沉重的增长压力，从而导致目的变成追求快速盈利，而非做好一个产品。

### 精力是最大的成本

对于大部分的独立开发者而言，在早期阶段都是一个人单打独斗，从产品的市场调研，到设计研发，最后到运营推广，每一个环节对于个人来讲都是非常消耗时间的，每个人的精力都非常有限，而对于独立开发者来讲，精力和时间就是最大的成本。我能给到的建议是，在不熟悉的领域善用 GenAI 来辅助，在产品早期阶段尽量使用成熟的 Saas 服务和各类 Pass 平台。

除了纯粹的移动端的工具开发者只需要考虑发布应用外，大多数的开发者需要考虑搭建官方网站、构建后端 API，考虑认证、授权、支付、统计、存储等等一系列的需求。这些需求在一定程度上都需要部署和托管，所以推荐大家使用云服务商来构建服务。

>  写到最后，笔者忽然认为自己并没有什么实际的成功经验，更多的是基于构建开源 demo 和从国外论坛成功学习来的经验，主要是面向海外的访问流量，所以对于上面的建议，大家需要根据自己的需求来进行合理判断，聚焦自己的业务需求来进行技术选型。
>
> 也欢迎大家在下方的评论区留言，分享你的独立开发技术栈和云服务商的选择，让我们一起交流学习。


## References

- https://nextjs.org
- https://pages.cloudflare.com
- https://nextra.site/docs/guide/ssg
- https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-nextjs-site/
- https://tailwindcss.com/
- https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/why-shadcn-ui-is-so-popular-in-2023-0f95e66f3ddc
- https://www.radix-ui.com/
- https://github.com/t3-oss/create-t3-app
- https://ui.shadcn.com/blocks
- https://developers.cloudflare.com/workers/platform/pricing/#workers
- https://next-auth.js.org/
- https://blog.cloudflare.com/prisma-orm-and-d1
- https://www.cloudflare.com/zh-cn/products/distributed-sql-database/
- https://neon.tech/
- https://supabase.com/
- https://clarity.microsoft.com/
- https://developer.chrome.com/docs/lighthouse/overview/