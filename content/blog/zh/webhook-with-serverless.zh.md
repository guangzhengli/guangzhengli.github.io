---
title: 用 serverless 开发一个 Chat 机器人提醒
date: 2022-12-19
tags: ["serverless", "cloudflare", "worker"]
featured: false
---

本文描述了笔者基于 Serverless 技术开发 Chat 机器人提醒应用的全流程，记录了开发遇到的一些问题和技术选择的思考。在开发完成后，有小伙伴想知道一些技术细节来做二次开发，于是有了这篇分享。

<!--more-->

## 业务需求

最开始想开发一个 Chat 机器人提醒，是因为当时项目上的人很多，每天都需要轮换站会和 Code Review 的 Owner，导致每天的站会和 Code Review 时，都需要先花费几分钟时间确认今天的站会 Owner 是谁，接着确定后还要准备 Share 屏幕，最后还需要手动维护项目人员轮换名单，会议的体验很差。为了解决以下的常规会议的痛点：

- 杜绝会议开始时常见的灵魂问题，今天的 owner 是谁
- 缩短会议的准备时间，预防 owner 没有提前去会议室准备的情况
- 预防到时间后，有人忘记参加会议，需要人手动提醒

所以有了第一个想法💡，即做一个自动提醒的机器人，包括有如下功能来缓解痛点：

- 定时发送消息
  - 在工作日时，在会议开始前5分钟时，发送提醒文本。
  - 在周末，不发送消息

- 当发送文本消息时，文本包括会议地址，owner，和会议时间。

- 定时轮换 owner 名单，定时可配置，例如每天轮换，或者每周轮换。

例如站会的提醒效果图如下所示：

![screenshotr_2023-2-19T17-12-28](https://storage.guangzhengli.com/images/screenshotr_2023-2-19T17-12-28.png)

## 技术选型

在业务需求确定后，首先需要判断系统是否要求高可用、可扩展或者性能是否有要求。在这个用例中，这些技术要求都可以不要，毕竟只是自用的辅助手段，就算偶尔挂了也无所谓。这也为后续的技术选型打开了道路。

其次需要预估该功能的资源需求，该功能每天定时发送的请求给 Chat 一般不超过 10 条，所以假设 `Request / Day = 10`。存储的话需要存储会议的轮换名单，名字占用很少，所以预估最大就是 1KB `Size of storage = 1KB`。

得到技术需求和资源预估后，就能在后续技术选型时有比较明确的方向。首先列出几个可以完成这个功能的方案：

- Local machine + Disk
- Cloud VM + Disk
- Serverless
  - AWS Lambda + DynamoDB
  - Azure Function + CosmosDB
  - Cloudflare Worker + Worker KV

## Local machine & Disk

首先我们尝试使用 Python 或者任意语言在本地环境进行 Schedule 定时，直接使用本地环境进行部署运行，因为不要求高可用，偶尔的服务宕机可以接受，所以在追求最简单的方式来实现也未尝不可。

方案是使用 Python schedule 库或者任意语言定时发送消息，使用本地文件来存储轮换名单。优点是在本地部署调试和管理起来比较方便，不需要额外的花费，缺点是必须保证  Python 定时进程存活和网络可用，一旦电脑关机或者你请假时就不可用了🤔。如下代码 Demo 是从同目录下 `code_review.txt` 文件中拿到当前会议 Owner 名字和下一个 Owner 的名字，一起发送 Code Review 提醒给企业微信。

```python
from posixpath import split
from corpwechatbot.chatbot import CorpWechatBot
import schedule
import time
from datetime import datetime

message = """Code Review马上开始啦
-----------------------------
ZOOM URL: https://xxx.zoom.us/j/xxx
    ID: xxx
    Passcode: xxx
-----------------------------
今天的 host 是: {current_owner}
明天的 host 是: {next_owner}
"""

def get_code_review_owner_name():
    f = open('code_review.txt', 'r', encoding='utf-8')
    name_array = f.read().split()
    current_owner = name_array[0]
    next_owner = name_array[1]
    name_array.insert(len(name_array), name_array.pop(0))
    write_txt = ' '.join(name_array)
    f = open('code_review.txt', 'w', encoding='utf-8')
    f.write(write_txt)
    f.close
    return current_owner, next_owner

def send_notification():
    current_owner, next_owner = get_code_review_owner_name()
    send_message = message.format(current_owner=current_owner, next_owner=next_owner)
    bot = CorpWechatBot(key='key providerd by wechat work')
    bot.send_text(content=send_message, mentioned_list=['@all'])

schedule.every().monday.at("17:00").do(send_notification)
schedule.every().tuesday.at("17:00").do(send_notification)
schedule.every().wednesday.at("17:00").do(send_notification)
schedule.every().thursday.at("17:00").do(send_notification)
schedule.every().friday.at("17:00").do(send_notification)

while True:
    schedule.run_pending()
    time.sleep(1)
```

### Cloud VM & Disk

第二个方案使用云服务器来运行定时进程，和第一个方案使用的技术栈和代码相同，可以用任意语言定时发送消息，只需要注意服务器时区问题即可。区别在于该方案需要购买云服务器来部署，不用在意关机或者不可用问题，但是在目前这种技术需求和资源需求下，去买一个云服务器完成该功能肯定是不划算的。

以上两种方案都有各自的优缺点，一种是可用性不高，一种是费用高，所以接下来最后一种 Serverless 方案，即是可以解决这两种问题的方案，也是我们今天主要需要讲的方案。

## Serverless

最后一种也是今天主要聊的方案，即使用无服务器计算的方式来完成定时计算，发送消息给 Chat Webhook 服务器。

无服务器计算（serverless computing），是一种由云服务商（AWS，Azure 或 Cloudflare）负责通过动态分配资源来执行一段代码的执行模型，并且仅收取运行代码所使用资源的费用。该代码通常运行在无状态的容器中，能够被包括HTTP请求、调度事件（cron任务）等各种事件触发并运行。因为代码运行在无状态的容器中，所以无法存储数据，对此云服务商提供了不同类型的存储服务和 SDK，可以无缝衔接对应厂商提供的 Serverless。

在之前分析技术需求时，我们了解到该应用的 `Request / Day < 10`，每天所需的 CPU 时间很少，所以使用 serverless 来执行定时发送消息的任务非常的合适。主要原因一是基础设施由云服务商提供，可用性方面有一定的保证，二是价格非常便宜(这也是 serverless 普及的最重要的原因)。下面是各大云服务商提供的每月免费额度，我们这个项目每个月用不到免费额度的万分之一。

- AWS Lamda: 128M + 3,200,000S / 1 month
- Azure Function: 1 million requests / 1 month
- Cloudflare Workers: 100,000 requests / 1 month

由于 serverless 是不提供存储功能的，所以需要存储人员轮换名单的话，光靠 serverless 的无状态容器就不够用了。需要用到各大云服务商提出的不同类型存储服务，比如有关系型数据库、NoSQL、KV，甚至是基于 SQLite 的 `serverless database`，例如 [Cloudflare D1](https://blog.cloudflare.com/introducing-d1/)。回顾我们需要存储的数据，只是简单的团队人员名单，所以无需使用结构化的数据，只需要很简单的 KV 存储即可。如下面几个 KV 服务和其对应的云厂商的免费额度：

- AWS DynamoDB：25GB Storage + 1G Networks
- Azure CosmosDB: 1000 request per-second + 25GB storage
- Cloudflare Workers KV: 1GB storage

由于 Cloudflare 原生提供绑定域名和提供 `serverless database`，所以笔者比较喜欢使用 Cloudflare Workers (不过可用性方面个人认为不如 AWS，偶尔会定时无效)。这里就以 Cloudflare Workers 为例展示功能代码，代码主要分为两个 `serverless application`，也就是两个定时任务，一是定时发送消息给 Chat 提供的 webhook 地址，另一个是定时轮换主持会议的 owner 名单。

首先是定时发送消息给 Chat 提供的 webhook 地址，从 KV 中获取当前的会议 Owner，会议地址和消息，通过获取配置的 webhook 地址发送给对应的 Chat（企业微信或者 Google Chat）。

```javascript
import { send_google_chat_message } from "./google_chat_notificaiton";
import { send_wework_chat_message } from "./wework_chat_notificaiton";

export default {
	async scheduled(controller, env, ctx) {
		var standup_owner_name = await get_owner_name(env, env.KV_STANDUP_OWNER_NAMES);
		var message = `今日的站会马上开始!!!\n今日站会 owner 是: ${standup_owner_name}\n会议地址是: https://zoom.us/j/xxxx`;
		if (env.MESSAGE_TYPE == 'GoogleChat'){
			send_google_chat_message(env, message);
		} else if(env.MESSAGE_TYPE == 'WeworkChat') {
			send_wework_chat_message(env, message);
		}
	},
};

async function get_owner_name(env, type) {
	var namesString = await env.notification_namespace.get(type);
	var names = namesString.split(',');
	return names[0];
}
```

二是定时轮换人员名单，这里主要是因为 owner 人员的轮换和会议主持时间可能不同步，例如每周轮换一次 Owner，明天发送 owner 主持的消息，所以需要单独的一个定时。下面代码实例展示周五发送轮换 owner 的消息。从 KV 中获取下一个 onwer 列表，轮换名单，并且 push 到 KV 中以便后续发送会议消息时拿到 owner 名单。

```js
import { send_google_chat_message } from "./google_chat_notificaiton";
import { send_wework_chat_message } from "./wework_chat_notificaiton";

export default {
	async scheduled(controller, env, ctx) {
		var next_standup_owner_name = await getAndRotateOwners(env, env.KV_STANDUP_OWNER_NAMES);
		var message = `Happy Friday! 别忘记填写 timecard 哦!!!\n下周站会 owner 是: ${next_standup_owner_name}`;
		if (env.MESSAGE_TYPE == 'GoogleChat'){
			send_google_chat_message(env, message);
		} else if(env.MESSAGE_TYPE == 'WeworkChat') {
			send_wework_chat_message(env, message);
		}
	},
};

async function getAndRotateOwners(env, type) {
	var namesString = await env.notification_namespace.get(type);
	var names = namesString.split(',');
	names.push(names.shift());
	var storeNameString = names.join(',');

	await env.notification_namespace.put(type, storeNameString);
	return names[0];
}
```

完整的项目代码放在这个 [仓库](https://github.com/guangzhengli/workers-webhook-notification) 👈

## 总结

回顾整个开发流程，我们能很轻易的感受到 serverless 的易用性和潜力，像类似于 WebHook、数据统计分析、Trigger 及定时任务和 Chat 机器人等功能，借用于 serverless 可以轻易的实现和部署，完成系统的原型设计和想法的闭环。近几年业界也在不断完善 serverless  的开发流程和核心缺陷，例如 [JVM 冷启动问题](https://www.infoq.com/news/2022/12/aws-lambda-snapstart-accelerate/)，[serverless 观测和调试增强](https://docs.aws.amazon.com/lambda/latest/dg/telemetry-api.html)，数据库 serverless 化如[wunderbase](https://github.com/wundergraph/wunderbase)，[Neon](https://neon.tech/?utm_campaign=newsletter)，[planetscale](https://planetscale.com/)，[D1](https://blog.cloudflare.com/introducing-d1/) 等项目。假以时日，当这些问题慢慢解决和优化，serverless 广阔的前景和强大将刷新人们的认知。

## Reference

1. https://sst.dev/chapters/zh/what-is-serverless.html
2. https://blog.cloudflare.com/introducing-d1/
3. https://aws.amazon.com/free/
4. https://azure.microsoft.com/en-us/pricing/free-services/
5. https://www.infoq.com/news/2022/12/aws-lambda-snapstart-accelerate/
6. https://github.com/wundergraph/wunderbase
7. https://www.sqlite.org/serverless.html
8. https://github.com/neondatabase/neon

