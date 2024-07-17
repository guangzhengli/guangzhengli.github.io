---
title: Independent Development Tech Stack 2024
date: 2024-03-25T22:00:00+08:00
tags: ["indiehacker", "tech-stack", "cloud"]
series: ["share"]
featured: true
math: false
---

The purpose of writing this article is to celebrate reaching 10K followers on X/Twitter 🎉. Suddenly, I felt the urge to share. In today's world, where the choice of tech stacks and cloud service providers is so rich and diverse, what tech stack should we choose if we need to develop independently?
## Why Independent Development?

Why do I limit my technology choices to independent development? Because if we are developing for a company or a client, I feel that the room for technological choices is limited, and the fun is also reduced.

First, except for a few startups, most existing companies or projects already have a well-established tech stack and standards. They usually recommend refactoring on the old tech stack or phasing out old microservices and introducing new technologies in new microservices, rather than completely overhauling everything for the sake of a novel tech stack.

Even if new tech stacks are introduced into old projects, the priority is given to tech stacks that facilitate team collaboration, such as those that are easy to hire for and conducive to teamwork.

So, if you want to satisfy your technical enthusiasm, the best way is to join open-source projects or develop your own independent projects.

Alright, let's officially start the recommendations!

## Frontend

### [Nextjs](https://nextjs.org/)

If you are mainly building frontend applications or projects that combine frontend with lightweight backend APIs, the most recommended framework is [Nextjs](https://nextjs.org/), an open-source framework based on React.

Reasons for recommendation:

- Gentle learning curve with very friendly official tutorials: [https://nextjs.org/learn](https://nextjs.org/learn)
- Rich ecosystem with many scaffolding options to meet different needs, such as the open-source scaffolding project [create-t3-app](https://github.com/t3-oss/create-t3-app), which can selectively integrate authentication, ORM, UI, and other functionalities and tech stacks.
- Simple deployment, as it is developed by the parent company of [Vercel](https://vercel.com/), making it very smooth to deploy on [Vercel](https://vercel.com/).

Why recommend [Nextjs](https://nextjs.org/) right off the bat? Firstly, [Nextjs](https://nextjs.org/) itself is a framework that allows for quick creation and deployment, enabling us to get our projects online quickly. Secondly, [Nextjs](https://nextjs.org/) is very friendly and convenient for integrating the tech stacks and platforms we will recommend later.

For example, if you want to deploy a static website, such as documentation or tutorials, you can use [Nextjs](https://nextjs.org/)'s Static Site Generation (SSG) along with some CDN hosting services. Here, I highly recommend [Cloudflare Pages](https://pages.cloudflare.com/), the only cloud service provider I give a five-star recommendation. For instance, the [nextra](https://nextra.site/) project is an open-source documentation project based on [Nextjs + SSG](https://nextra.site/docs/guide/ssg).

Of course, you can also deploy some full-stack projects that require lightweight APIs, such as blogs or AI chatbots. You can use [Nextjs](https://nextjs.org/)'s Server Side Rendering (SSR) and API Routes, along with cloud functions from cloud service providers like [Vercel](https://vercel.com/)'s [Serverless Functions](https://vercel.com/docs/functions) or [Cloudflare Worker](https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-nextjs-site/), to quickly deploy a full-stack project.

For example, the blog of [leerob](https://leerob.io/), a core member of [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/), is a great example. His personal blog is built with SSR + API Routes, featuring functionalities like comments and subscriptions, and is deployed on [Vercel](https://vercel.com/).

For independent developers, the most important thing is to get our projects online quickly, rather than spending a lot of time reinventing the wheel, deploying, and maintaining. Therefore, [Nextjs](https://nextjs.org/) + [Vercel](https://vercel.com/) is a very good choice. Even if Vercel raises prices or stops being free in the future, it can be replaced with other cloud service providers like Cloudflare.

### TailwindCSS + Shadcn/ui

When it comes to choosing a UI styling framework, there are many options. The richness of the frontend developer ecosystem is truly impressive, something unimaginable in other language ecosystems.

Here, I only recommend what I personally find comfortable to use: [TailwindCSS](https://tailwindcss.com/) + [Shadcn/ui](https://ui.shadcn.com/).

Reasons for recommending [TailwindCSS](https://tailwindcss.com/):

- Compared to traditional CSS, [TailwindCSS](https://tailwindcss.com/) is much more convenient with built-in syntactic sugar.
- Highly customizable with consistent code standards.
- Integrates well with modern frontend tools like PostCSS and PurgeCSS.

Another important reason for recommending [TailwindCSS](https://tailwindcss.com/) is that in today's world of code generation tools like GPT and Github Copilot, AI can generate [TailwindCSS](https://tailwindcss.com/) + JSX code very well, making it an AI-friendly coding style.

Reasons for recommending [Shadcn/ui](https://ui.shadcn.com/):

- Highly flexible UI framework that works well with [TailwindCSS](https://tailwindcss.com/).
- Beautifully designed styles with plenty of code samples available, such as those in [blocks](https://ui.shadcn.com/blocks).

Unlike common component libraries like Ant Design and Chakra, shadcn/ui is a project that uses Tailwind CSS to wrap [radix-ui](https://www.radix-ui.com/) (a headless UI component library). This approach is a double-edged sword: the advantage is high customizability and on-demand usage, but the downside is that installation and updates are not convenient, and future updates may be affected by radix-ui.

Another reason for recommending shadcn/ui is related to AI. The author of shadcn/ui has joined [Vercel](https://vercel.com/) to develop [V0](https://v0.dev/), an AI frontend code generation tool, so I am optimistic about the future of shadcn/ui.

Overall, you can choose shadcn/ui as needed. For more details on shadcn/ui, you can read [this article](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/why-shadcn-ui-is-so-popular-in-2023-0f95e66f3ddc).

Other common UI frameworks worth trying include [daisyui](https://daisyui.com/) and [chakraui](https://chakra-ui.com/).

### Others

- [Prisma](https://www.prisma.io/): A database ORM that, while not the best in performance, has the best ecosystem with support from various databases and cloud providers. Convenient for switching databases when data volume increases.
  - [Drizzle](https://orm.drizzle.team/): If you choose a Serverless solution, consider using Drizzle ORM, which is optimized for Serverless scenarios and is only 31 KB after packaging.
- [NextAuth.js](https://next-auth.js.org/): An authentication library for Nextjs. Although the documentation is a bit chaotic, its ecosystem and functionality are the best.
- [SWR](https://swr.vercel.app/): An HTTP request library from the Nextjs team that solves component data fetching and caching issues.
- [zod](https://zod.dev/): A schema validation library that can be used for frontend and backend data and form validation.
- [Zustand](https://zustand.surge.sh/) & [jotai](https://jotai.org/): Zustand is a simple React state management library that can replace Redux. Jotai is an atomic state management library, and you can choose based on your needs.
  - For state management library choices, I highly recommend this video: [React Query Essentials](https://www.youtube.com/watch?v=5-1LM2NySR0). For articles on state management, I recommend this series: [practical-react-query](https://tkdodo.eu/blog/practical-react-query).
- [driverjs](https://driverjs.com/): A library for guiding user operations, useful for onboarding and operation guides.
- [wxt](https://github.com/wxt-dev/wxt): A browser extension development framework similar to Nuxt, useful for developing Chrome, Firefox, and other extensions.
- [orama](https://oramasearch.com): A frontend full-text search solution that can replace Algolia. The new Node.js website uses this search engine.

## Backend

After discussing frontend libraries and platform choices, let's move on to the backend. In fact, there are fewer pure backend options. Since backend services require CPU, memory, bandwidth, and other resources, which are not cheap, I recommend Serverless solutions. If you prefer familiar backend frameworks, you can use platforms like [Railway](https://railway.app/) or [Fly.io](https://fly.io/) to deploy containers.

### Cloudflare Worker

Choosing Serverless is the most cost-effective solution because everything is pay-as-you-go. This means you don't have to worry about service costs in the early stages of your independent project. The only thing to be cautious about is cloud service platforms that lure you in with free offers and then charge you heavily once you're deeply invested. Switching Serverless platforms can be quite troublesome. So, if you want to choose a Serverless solution, my only recommendation is [Cloudflare Worker](https://workers.cloudflare.com/).

The [Free Plan](https://developers.cloudflare.com/workers/platform/pricing/#workers) offers 100,000 requests per day, which is sufficient for most projects. The only downside is the 10ms CPU execution time in the Free Plan, which is very short and can cause many computations to time out. However, the $5/month [Standard Plan](https://developers.cloudflare.com/workers/platform/pricing/#workers) is not expensive and increases the CPU execution time to 30s with ample time quotas, which is sufficient for most projects.

Moreover, [Cloudflare Worker](https://workers.cloudflare.com/) integrates well with other Cloudflare services we will recommend later, such as KV (key-value database), Pages (static pages), R2 (object storage), D1 (relational database), Queue (MQ queue), etc. Most of these services can be used for free and share the Standard Plan quota.

After choosing [Cloudflare Worker](https://workers.cloudflare.com/), you can use native Node.js because its Worker code is based on the V8 engine, allowing you to write your Worker code in JavaScript, TypeScript, Rust, and other languages.

You can also use Serverless frameworks like [hono.dev](https://hono.dev/), which is a web framework for edge runtime, offering many out-of-the-box features like routing and middleware. It also provides compatibility with different Serverless platforms like [Vercel](https://vercel.com/) and [AWS Lambda](https://aws.amazon.com/lambda/).

### Railway & Fly.io

If your project is not suitable for a Serverless solution, as Serverless has some drawbacks like CPU time, usability, and debugging issues, you can choose your familiar backend language and opt for container deployment.

Regarding backend languages and frameworks, I won't make specific recommendations here because the choice depends more on your familiarity. Due to work and open-source projects, I have used various backend frameworks like Java/Spring, C#/.NET, Ruby/Rails, Node.js/Express, Python/FastAPI, and Go/Gin. Each framework has its pros and cons, so I recommend using the one you are most familiar with and has a good open-source ecosystem. Familiar frameworks can improve development efficiency, especially when deployment costs are similar.

I recommend using container deployment solutions rather than managing host deployments yourself. For container deployment platforms, I recommend [Railway](https://railway.app/) and [Fly.io](https://fly.io/). These platforms offer smooth container deployment and management, and you can deploy databases and other services as well. The reason for recommending these platforms is that they have given me a $5 monthly free quota due to my active participation in open-source projects, and I found them quite good after using them.

## Auth

Regarding Auth-related frameworks and cloud services, my personal experience is that if your business auth requirements are not complex and you are using a tech stack like Nextjs + [NextAuth.js](https://next-auth.js.org/), then directly using [NextAuth.js](https://next-auth.js.org/) to integrate social logins like Google OAuth, Github OAuth, Azure AD, and JWT authentication is the most convenient.

You don't need to manage user passwords or be responsible for login security. Simple configuration and code can achieve this. More importantly, you don't need to integrate other cloud services, reducing complexity and cost.

Traditional cloud services like [Auth0](https://auth0.com/) or newer ones like [Clerk](https://clerk.com/) have good functionality and ecosystems, but their free plans are limited, and subsequent charges are very expensive, so I don't recommend using them.

### [Supabase Auth](https://supabase.com/auth)

If your business auth requirements are more complex, such as needing custom login, registration, password recovery, 2FA authentication, etc., I recommend using [Supabase Auth](https://supabase.com/auth). It supports 50,000 monthly active users, is stable, and user management can be integrated with the database. It also supports social logins, email-password logins, 2FA, and other features.

### [Cloudflare Zero Trust](https://developers.cloudflare.com/cloudflare-one/)

If your user base is very small (less than 50 people) or you need to provide secure access to your project for partners, family, or friends, I highly recommend using [Cloudflare Zero Trust](https://www.cloudflare.com/zh-cn/teams/zero-trust/). Zero Trust is a security service based on Cloudflare that allows you to restrict access to your project to specified user emails or fixed IPs through simple configuration without modifying a single line of code.

The downside of this solution is that your domain must be hosted through Cloudflare, and the free quota of Cloudflare Zero Trust is 50 users, with very high costs beyond that. So, this solution is only suitable for small teams or personal use.

However, the advantage is very obvious: no additional code or services are needed, just simple configuration to make your project securely accessible. It is very suitable for private projects, such as setting up GPT projects like [Lobe Chat](https://github.com/lobehub/lobe-chat) or [ChatGPT-Next-Web](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web) for family and friends. You can even use Cloudflare R2 to set up a private image hosting service, but I won't go into that here.

## Database

Choosing a database is very important and diverse because there are many database options (MySQL, PostgreSQL, MongoDB, SQLite, Redis, etc.), and the competition among cloud service providers adds to the complexity. Here, I only recommend some useful cloud service providers.

### Supabase Database

For traditional relational databases, I recommend using PostgreSQL. The open-source community of PostgreSQL has always been the best, excelling in functionality, usability, and open-source ecosystem compared to MySQL.

The corresponding cloud service provider is [Supabase](https://supabase.com/), a highly praised database provider. It offers two free database projects with 500MB of storage, supports all Postgres features, and has many plugins and tools, such as Vector [vector databases](https://guangzhengli.com/blog/zh/vector-database/). Although subsequent charges are not cheap, the free and standard versions are sufficient for independent developers in the early and mid-stages. If it becomes insufficient later, you can consider setting up your own database on Fly.io and migrating.

The Supabase team is very active, and the open-source community is excellent. In addition to [Supabase Auth](https://supabase.com/auth) mentioned earlier, they also offer services like [Supabase Storage](https://supabase.com/storage), [Supabase Realtime](https://supabase.com/realtime), and [Supabase Functions](https://supabase.com/functions), all of which are good services.

Similar to Supabase Database, Serverless Postgres like [Neon](https://neon.tech/) is also good.

### Cloudflare D1

If you want a cheaper database that can support stable project operation, I recommend using [Cloudflare D1](https://www.cloudflare.com/zh-cn/products/distributed-sql-database/), an edge SQLite database. Although SQLite is not as powerful as Postgres, it can be deployed on CDN nodes worldwide, meaning your database can be closer to users, reducing latency and improving speed.

Recently, I saw that D1 can integrate with [Prisma](https://www.prisma.io/), meaning you can use Prisma ORM to operate the D1 database, which is very convenient for full-stack projects. For more details, you can read this [blog](https://blog.cloudflare.com/prisma-orm-and-d1).

The biggest advantage of choosing D1 is its low cost. The free quota is 5GB, and subsequent costs are only $0.75/month. You can also use Cloudflare KV, R2, Queues, and other services, which integrate well with Cloudflare Worker.

### Upstash

If your project needs lightweight Redis usage, I recommend using [Upstash](https://upstash.com/), which can be used to store hot data, cache, etc. This cloud service provider also offers services like Kafka and vector databases. Subsequent charges are also pay-as-you-go, making it a recommended option.

## Payment

There are not many choices for payment service providers because payment involves security, taxation, financial regulation, and legal issues. It is not recommended to reinvent the wheel or use niche cloud service providers. Here, I only recommend the most suitable ones.

### Stripe

[Stripe](https://stripe.com/) is widely recognized as the best payment service provider globally, supporting most currencies and offering reasonable fees. From a development perspective, it has comprehensive documentation and the most complete open-source ecosystem.

I don't want to spend too much time on [Stripe](https://stripe.com/). Its advantages are obvious, but so are its disadvantages. Most people in China find it difficult to register and use Stripe. The most reliable way to register Stripe is to set up a company abroad and register Stripe through the company.

However, for independent developers, running a company abroad is not easy. While registering a company through certain channels is relatively easy, dealing with the company's tax and legal issues is more complex. So, I only mention it here. If you have the conditions to register Stripe, I recommend using Stripe.

## Statistics

When it comes to data observation, there are generally two types of data analysis: business data analysis and technical data analysis. Business data analysis includes metrics like website traffic, page clicks, and popular features. Technical data analysis involves aspects like website performance and error log analysis. Here are some recommended data analysis tools.

### Website Analysis

For website analysis, I recommend using [Umami](https://umami.is/). It's an open-source website analytics tool that can be hosted for free on Vercel + Supabase. By integrating a script, you can collect your own data without worrying about data leaks and privacy issues.

Currently, this blog's data analysis is done using [Umami](https://umami.is/). You can check out the results by clicking on this [link](https://analytics.guangzhengli.com/share/o3zAba1V/guangzhengli).

If you need more powerful data analysis features, such as user behavior recording and website heatmap analysis, I recommend using Microsoft's [Clarity](https://clarity.microsoft.com/). It claims to be a free website analytics tool forever. Microsoft collects data to feed its AI but does not share it with third parties.

Similarly, Google Analytics is also a good choice. If you have the time, consider integrating multiple tools. Having data is essential to extract useful information.

### Analysis Tools

- Logs: Log Analysis
  - [NewRelic](https://newrelic.com/) offers 100GB of usage, which can be used to record logs, APM, analyze error logs, API performance, etc.
  - [Betterstack](https://betterstack.com/logs/pricing) offers 1GB/month and is often used for integrating log analysis in pure frontend projects.
  - [Sumologic](https://www.sumologic.com/pricing/) is a recently discovered log platform that works quite well.
- Performance: Performance Analysis
  - [PageSpeed](https://pagespeed.web.dev/) by Google, PageSpeed Insights can be used to analyze frontend web performance.
  - [LightHouse](https://developer.chrome.com/docs/lighthouse/overview/) by Google, a tool to improve web quality, including but not limited to analyzing web performance, SEO, accessibility, etc.
- SEO: Search Engine Optimization
  - [Google Search Console](https://search.google.com/search-console/about) is Google's official SEO tool that can be used to analyze a website's performance on Google search.
  - [META SEO Inspector](https://chromewebstore.google.com/detail/meta-seo-inspector/ibkclpciafdglkjkcibmohobjkcfkaef) is a Chrome extension that can be used to analyze a webpage's SEO information. SEO might not be well-known among domestic developers, but in many overseas business scenarios, SEO's importance can determine a project's success.

## Others

### IAC (Infrastructure as Code)

For independent developers, it's common to use various cloud service providers. For example, AWS for sending emails using SES, Azure for some Identity functions, Cloudflare for certain services, and NewRelic for creating corresponding log sets. Each cloud service provider has its own API and CLI tools, but switching between multiple providers can be mentally taxing.

Therefore, I recommend using IAC tools to manage everything uniformly. Here, I recommend [Pulumi](https://pulumi.com/). [Pulumi](https://pulumi.com/) supports managing your cloud services using code. The main reasons for recommending it are its support for mainstream cloud service providers, multiple languages (JS, TS, Python, etc.), and extensive community support.

### Other Development Tools (These are the ones I remember for now)

- [Buildkite](https://buildkite.com/): An additional pipeline tool. If your chosen cloud service provider does not offer CI/CD services or cannot integrate with GitHub Actions, consider using [Buildkite](https://buildkite.com/).
- [Bruno](https://www.usebruno.com/): A local HTTP request tool similar to Postman, useful for debugging API requests.
- [Proxyman](https://proxyman.io/): A proxy capture tool that can be used for debugging, forward proxy, reverse proxy, etc.

## Postscript

Recently, while reading the book "Small and Beautiful: The Rules of Sustainable Profitability," I found several interesting points made by the author. The author believes that if you want to be an independent developer and run a product independently, the most important things to focus on are quick delivery, early charging, and not spending money until the last moment.

First, quick delivery means rapid product iteration and quick trial and error. Creating a product is a process of gaining experience through fast feedback loops and iterations. If the product can be released to consumers early, their communication and feedback can help individual developers avoid the pitfall of working behind closed doors. Compared to spending a year building an initial version and then releasing a major update every quarter, shortening the product build time to a month and releasing small versions weekly or even daily can help independent developers reduce trial and error costs and quickly find a path to profitability.

Secondly, early charging is also crucial. Even if your product charges just $1, it is fundamentally different from being free. Independent developers can offer tiered pricing starting from free, but it should never be completely free. Free products can drag down operating costs and make it difficult to assess the product's actual value. As economist Dan Ariely wrote in "Predictably Irrational," "People will flock to free things, even if it's not what they want."

Once the product starts charging, consumers will seriously consider whether it brings them real value and solves their actual problems. Charging and pricing are good evaluation standards to help independent developers determine if their product has real value. Otherwise, developers should consider whether to shift their focus to other products that can bring more real value.

The last suggestion is not to spend money until the last moment, which is also the theme of this article. Although the book advises independent developers not to rely on advertising and burning money to achieve rapid growth, I believe that reducing costs is a crucial consideration for independent developers. Otherwise, high costs can bring heavy growth pressure, leading to a focus on quick profitability rather than creating a good product.

### Energy is the Biggest Cost

For most independent developers, the early stage is a solo effort. From market research to design and development, and finally to operation and promotion, each step is very time-consuming for an individual. Everyone's energy is limited, and for independent developers, energy and time are the biggest costs. My advice is to use GenAI to assist in unfamiliar areas and to use mature SaaS services and various PaaS platforms in the early stages of the product.

Apart from pure mobile tool developers who only need to consider publishing applications, most developers need to consider building a website, constructing backend APIs, authentication, authorization, payment, statistics, storage, and a series of other requirements. These needs often require deployment and hosting, so I recommend using cloud service providers to build services.

> In conclusion, I suddenly realized that I don't have much actual success experience. Most of my experience comes from building open-source demos and learning from successful cases on foreign forums, mainly targeting overseas traffic. Therefore, you should make reasonable judgments based on your own needs and focus on your business needs when choosing technology.
>
> Feel free to leave comments below to share your independent development tech stack and cloud service provider choices. Let's learn and exchange ideas together.

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
