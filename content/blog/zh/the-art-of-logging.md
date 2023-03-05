---
title: 日志的艺术
date: 2023-02-28T20:33:36+08:00
tags: ["log", "developer"]
series: ["The art of code"]
featured: true
---

理解日志并不是一件容易的事，开发人员在编写代码之时往往会纠结在某处打印的日志是不是有意义的，而 SRE 在面对缺少日志的生产问题时往往一筹莫展，Ops 在对面海量日志时往往需要花费更多的精力来维护，而项目的实际管理者在面对毫无实际业务价值的日志时，往往不想花费太多的人力和财力去管理它。

因此，在开发应用程序时遵循良好的实践，在收集管理日志时选用成熟的方案，往往能让这些矛盾得以缓解，这也就有了这一篇的分享。

<!--more-->

## 矛盾的开始

首先介绍的是为什么需要记录日志，日志的作用。其实关于日志的作用无需介绍太多，因为大多数的开发人员在调试代码问题时，解决不同环境的 Bug 时都有很明确的感受以及强烈的需求。日志作为调试的助手，生产环境的救星。笔者只见过嫌弃日志打的太少的，几乎没有见过嫌弃日志打的太多的开发和运维人员。通过查询日志的方式来确定代码的分支走向，API是否请求正确，核心业务的数据是否正确，是否有错误的堆栈信息，这些都构成开发和运维人员判断代码和生产问题的第一手段。笔者难以想象如果一个复杂庞大的系统没有记录任何日志，该如何排查生产环境的 Bug。

如果有如此强烈的需求，那每一行代码都打日志来记录上下文不就行了吗？这样无论什么环境有什么代码有问题，通过搜索日志都可以排查出来。理论上这样确实可行，但是有一些问题目前无法解决，一是日志存储量的问题，常见的中大型系统日志大概在 TB 级，超大型系统的日志大概在 PB 级，根据 [Cloudflare提供的数据](https://blog.cloudflare.com/log-analytics-using-clickhouse/)，它每秒大概处理 4 千万的请求，这对于存储的费用来讲是一个巨大的挑战。二是搜索的性能下降，像 [Elasticsearch](https://github.com/elastic/elasticsearch) 数据库这类常见的日志存储方案，海量的日志会导致其所维护的映射关系爆炸式增长，即使划分不同的 Index，分布式管理不同的 ElasticSearch 集群，也很难做到搜索性能不随数据量的增加而下降。三是海量日志的生成会在峰期时拖慢系统性能，增大出故障的风险。

所以综上可以得出最简单的结论，即日志既不能打印太多导致存储和管理日志太难，也不应该因为打印太少导致运维人员无法排查问题，这听起来自相矛盾，但这就是关于日志的艺术！

## 日志级别

通常在我们打算记录日志前，我们需要判断打印日志的级别，在介绍如何确定日志级别前，我们先聊一聊日志级别的作用。首先第一点是大家所熟知的，日志级别可以帮助确定日志信息的优先级，可以有效的减少信息噪音和警报疲劳。二是在查询日志时，加到日志级别的过滤，能有效的查询到所需的日志。

常见的日志级别有以下几类，并且从低到高的顺序是：`TRACE`、`DEBUG` 、`INFO`、 `WARN`、`ERROR`、 `FATAL`。通常在本地环境，我们打印日志所配置的级别是 `Trace` ，即打印出所有级别的日志，但是在生产环境，会随着存储的压力，查询的性能和磁盘 IO 的影响，将日志的级别调整到 `INFO` 级别甚至是 `ERROR` 级别。这就意味着你在代码中低于该级别的日志都不会打印。

那这是不是代表我们在代码中只需要打印和生产环境匹配的日志级别呢？例如生产环境只打印 `INFO` 级别的日志，我们在代码中写 `DEBUG` 级别的日志并不会打印，也没有必要写了。有的项目管理者为了省下日志管理的费用，只在生产环境只打印 `ERROR` 级别的日志，那是不是 `INFO` 级别的日志也可以不写呢？代码中只打印和生产环境匹配的日志级别日志，这确实是笔者在大多数项目时看到的现状，但是现实情况也往往更加复杂，例如以下几个场景。

### 场景一

某工程师在调查生产环境中某个创建资源的 API 性能较低问题时，发现是由于该 API 在保存资源前做了写 `INFO` 级别的日志，将资源对象都写到日志中，由于资源的对象属性很多，所以导致在业务峰期时，代码打印出海量日志，耗尽Buffer区内存，从而拖慢主线程，造成服务性能整体下降。因此该工程师将该业务日志打印操作删除，以降低生产环境磁盘 IO 损耗，解决性能问题。

但是某天由于修改了该 API 服务调用链路上的某服务代码，导致该 API 创建出来的对象有错误，并且由于缺少了生产环境保存该资源时的日志，无法排查出是 API 的请求参数有问题，还是后续的计算逻辑有问题。这时我们只能重新修改日志级别，重新构建发布上线吗？

### 场景二

假设将生产环境的日志设置为 `ERROR` 级别。某一时刻，依赖的下游服务故障，导致请求大量超时。又由于在业务峰期 QPS 非常高的时期，短时间内集中产生大量的错误日志，导致磁盘IO急剧提高，耗费大量CPU，进而导致整个服务瘫痪。我们应该如何处理？

### 场景三

某工程师在排查生产问题时，发现 `INFO` 级别的日志还无法满足排查 Root Cause，有一个 `DEBUG` 日志级别的日志是他需要的，但是生产环境只有 `INFO` 级别，这时只能修改级别然后重新启动服务吗？

### 日志级别规范与动态调整

解决以上问题的方法，一是需要我们在项目中，明确日志级别的规范，不为了调试方便和减少存储随意使用日志级别。二是给日志级别加上**动态调整**的功能。也就是需要解决线上问题时，调低线上日志输出级别，获取全面的Debug日志，帮助工程师提高定位问题的效率。在生产日志海量增加拖慢服务性能时，调高线上日志输出级别，减少日志的生成，缓解磁盘 IO 压力和提高服务性能。

以下是对于日志级别给出的建议：

- TRACE：应该在开发期间使用它来追踪错误，但永远不要提交到版本控制系统（VCS）中。

- DEBUG：记录程序中发生的任何事情。主要在调试期间使用，建议在进入生产阶段之前缩减调试语句的数量，只留下最有意义的条目，并可以在故障排除期间激活。

- INFO：记录所有由用户驱动的事件或特定于系统的操作（例如定期计划操作）。

- WARN：此级别记录所有可能成为错误的事件。例如，如果一个数据库调用花费的时间超过预定义的时间，或者如果内存缓存接近容量。这将允许适当的自动警报，并在故障排除期间允许更好地了解系统在故障之前的行为方式。

- ERROR：在此级别记录每个错误条件。这可以是返回错误或内部错误条件的 API 调用。

- FATAL：代表整个服务已经无法工作。请非常节制地使用这个级别。通常此级别记录表示程序的结束。

## 记录日志

### 什么时候记录日志

什么时候记录日志并没有标准规范，需要开发人员根据业务和代码来自行判断，除了常规的记录事件，例如进行了哪些操作、发生了与预期不符的情况、运行期间出现未能处理的异常或警告、定期自动执行的任务外。笔者还建议在以下场景加上日志：

- 在调用第三方系统时，将调用 API 的 URL 带上 `Request/Response Body` 和异常都记录到日志。原因是当发生故障时，能够有明确且清晰的的日志报告说明故障原因，减少不同系统服务运维人员或者不同公司之间的**责任界定**，以更顺畅的方式推动问题的解决。
- 在重要核心业务的关键代码和分支加上日志，例如 if-then-else 语句，它可以帮助开发人员了解程序是否根据其当前状态遍历了预期路径。并且由于核心业务的数据普遍难以手动复现，了解代码分支的走向至关重要。
- 核心业务的审计日志，如果某业务和法律或合同具有关联性，给对应的操作加上审计日志是非常有必要的。并且存储日志要求强一致性数据库。
- 应用服务启动时输出配置信息。初始化配置的逻辑一般只会执行一次，不便于诊断时复现，所以应该输出到日志中。

### 日志属性

除了在日志常规需要打印的 `log level`，`timestamp`，`message`，`exception` 和 `stack trace` 外，排查问题往往还需要其它的字段来帮助定位和查找 Root Cause，常见的额外字段有以下几种：

- `trace id` 即服务链路追踪的唯一 ID。在请求进入到系统 7 层网关时，即在 HTTP header 中加上对应请求整个生命周期唯一的 `trace id`，并随着该请求调用一直携带。当请求链路过长，开发人员难以找到完整的请求日志时，`trace id` 有助于反向查找完整日志。
- `span id` 即表示 `trac id` 生命周期中拆分的单个操作。例如当请求到达每个服务后，服务都会为请求生成 spanid，第一个 spanid 称之为 root span，而随请求一起从上游传过来的 spanid 会被记录成 pspanid (parent-spanid)。当前服务生成的 spanid 随着请求一起再传到下游服务时，这个spanid 又会被下游服务当做 pspanid 记录。由此 `span id` 有助于当服务调用复杂时还原出整个请求的调用链路视图。
- `user id` 即用户的唯一 ID。确保当用户上报 Issue 或者提交 TIcket 的时候，可以根据当前用户的唯一 ID 直接查询对应错误日志，减少干扰项，缩短排查周期。
- `tenant_id` 这是租户 ID（如果存在）。对多租户系统非常有帮助
- `request uri` 即当前微服务请求 URI (用户一个业务操作可能对应着多个服务不同的 `request uri`)，当某业务出现问题时，通过该业务对应**入口**的 `request uri` 往往能很快拿到 `trac id`，再通过 `trac id` 去查找对应请求的日志往往能很快解决问题。
- `application name` 即当前微服务名称。有助于识别哪个服务生成了此日志，也有助于通过 `application name` 过滤日志，查询服务整体故障。
- `pod name` 即当前请求所在的 k8s 资源 Pod 名称（如果存在）。目前大多数微服务使用 k8s 来完成容器编排，打印 `pod name` 有助于当某个 `pod` 故障时，重启或者 Kill Pod。
- `host name` 即当前请求所在的机器名称。即使使用 k8s 托管微服务，也会出现由于 k8s 集群所在的某台机器出现磁盘或者网络故障时，服务无法正常工作的情况，打印 `host name` 有助于排查问题最后一公里，即由于机器硬件问题导致故障。

### 日志安全

日志需要保证日志框架的安全和敏感信息处理。框架安全指的是使用成熟的，经过大量生产环境验证的日志框架库，而非自己造轮子。敏感信息处理是大部分公司的生命线，请牢记日志的安全性和合规性要求：

- 不要泄露敏感的个人身份信息 (PII)。
- 不要泄露加密密钥或秘密。
- 确保公司的隐私政策涵盖日志数据。
- 确保日志提供商满足合规性需求。
- 确保满足数据存储时间要求。

### 日志的坏味道

- 使用中文或者非英文打印日志。
  - 英文表示日志将以 ASCII 字符记录。这一点特别重要，因为像中文经过一系列处理后，它可能因为字符集或者编码集最终无法正确呈现。
  - 英文自带分词效果，像使用 `ElasticSearch` 这类倒排索引存储引擎存储日志，中文日志不仅需要添加专门的分词器，并且存储和查询效果不如英文。
- 没有上下文的日志。类似直接打印 `Transaction failed` 或者 `User operation succeeds` 这类日志。因为在写代码时通过代码上下文能够理解日志消息，但是当阅读日志本身时，这个上下文不存在，这些消息可能无法理解。
- 将打印日志的操作放在循环当中。除非特定需求，否则打印出来的日志不仅难以阅读和查找，还会耗费大量存储资源。
- 引用慢操作数据，如果当前上下文中没有打印日志需要的数据，需要调用远程服务或者从数据库获取，又或者通过大量计算，那应该先考虑这项信息放到日志中是不是必要且恰当的。

## 日志的可观测性

最近十年因为微服务和云原生的相继崛起，收集存储和分析日志领域发生了重大的变化。早期我们无需进行日志的收集，当时将单体服务的所有日志存储在文件当中，使用 tail、grep、awk 来从日志中挖掘信息。但是在系统日益复杂的今天，这种方式已经无法满足我们的需求。为了应对日益复杂的日志管理需求，开源社区和工业界也发展出一些列的方案，例如最为流行的 [Elastic Stack](https://www.elastic.co/elastic-stack/) 开源解决方案，云厂商提供的一站式解决方案像 [AWS DataDog](https://www.datadoghq.com/product/log-management/) 和 [Azure Dynatrace](https://www.dynatrace.com/monitoring/platform/log-management-analytics/)。

无论使用哪种方案，日志管理都已经不再是一个简单的话题。在我们有明确感知的打印日志和查询分析日志之间，还包含着对日志进行收集、缓冲、聚合、加工、索引、存储等若干个步骤，并且每一步都蕴含着艰难曲折。

![azaofU](https://storage.guangzhengli.com/images/azaofU.jpg)

### 日志的收集

最早我们使用 [Elastic Stack](https://www.elastic.co/elastic-stack/) 中的 [Logstash](https://www.elastic.co/logstash/) 来进行日志的收集和加工。系统中不同的服务通过使用 tcp/udp 的协议，主动发送请求将日志推送到 Logstash 中，接着 Logstash 将日志进行转换加工(数据结构化)和输出。这种模式维持了很长一段时间，但是它也有比较严重的缺陷，那就是 Logstash 与它的插件是基于 JRuby 编写的，要跑在单独的 Java 虚拟机进程上，默认的堆大小就到了 1GB。如果需要部署成千上万个日志收集器，那么这种方案就显得太过沉重。所以后来 Elastic.co 公司使用 Golang 重写了一个功能较少，却更轻量高效的日志收集器 [Filebeat](https://github.com/elastic/beats/tree/master/filebeat) 才缓解了这一矛盾。

除此之外 [Fluentd](https://github.com/fluent/fluentd) 通常是配合 Kubernetes 时的首选开源日志收集器。它是 Kubernetes 原生的，可以使用 DaemonSet 的方式部署与 Kubernetes 无缝集成。它允许从不同的地方像 Kubernetes 集群、MySQL、Apache2 等收集日志，并解析发送到所需位置如 Elasticsearch、Amazon S3 等。Fluentd 用 Ruby 编写的，在低容量下运行良好，但当需要增加节点和应用程序的数量时，也会有会性能问题。

最后在日志收集时，有可能会因为业务峰期生成海量日志，影响服务稳定性和造成日志丢失。在这种情况下，我们还需要在 Logstash 或者存储日志数据库前加上一道缓冲层。在较小规模的系统中 [Redis streams](https://redis.io/topics/streams-intro/) 是一个较好的选择，如果面对的规模更大的数据，那么 Kafka 集群或者云厂商提供的消息队列解决方案将是不二之选。

### 结构化日志

在收集完日志后，我们还需要进行结构化的处理。因为日志是非结构化数据，一行日志中通常会包含多项信息，如果不做处理，那只能以全文检索的原始方式去使用日志，这样既不利于统计对比，也不利于条件过滤。像下面这一行是 Nginx 服务器的 Access Log。

```
10.209.21.28 - - [04/Mar/2023:18:12:11 +0800] "GET /index.html HTTP/1.1" 200 1314 "https://guangzhengli.com" Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
```

尽管我们已经习惯了默认的 Nginx 格式，但上面的示例仍然难以阅读和处理。我们可以通过 Logstash 或者其它工具将它转换成结构化的数据，例如 JSON 格式。

```json
{
  "RemoteIp": "10.209.21.28",
  "RemoteUser": null,
  "Datetime": "04/Mar/2023:10:49:21 +0800",
  "Method": "GET",
  "URL": "/index.html",
  "Protocol": "HTTP/1.1",
  "Status": 200,
  "Size": 1314,
  "Refer": "https://guangzhengli.com",
  "Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"
}
```

经过结构化后，例如像 ElasticSearch 这类倒排索引数据库可以针对不同的数据项建立索引，进行查询统计、聚合等操作。

除此之外还有一种工业界的做法像 [Splunk](https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/) 推荐将字段变为 key-value 对的形式放在同一个大的规范日志行中(logfmt)，如将 Nginx 日志作为规范日志行将变成如下这样：

``` 
remote-ip=10.209.21.28 remote-user=null datetime="04/Mar/2023:10:49:21 +0800" method=GET url=/index.html protocol=HTTP/1.1 status=200 size=1314 refer=https://guangzhengli.com agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"
```

这类数据经过 Splunk 存储后，可以通过使用内置的查询语言进行检索，像使用 `method=get status=500`  查询所有返回 500 响应的 GET 方法。使用 `method=get method=get status=500 earliest=-7d | timechart count` 查询语句得到过去 7 天返回 500 响应的 GET 方法总数量和图表。这种方式笔者使用的并不多，所以感兴趣的可以参考 [Stripe canonical-log-lines](https://stripe.com/blog/canonical-log-lines) 这篇文章获得更多细节。

### 日志的存储与查询

经过日志的数据结构化后，可以将数据存入数据库中并进行查询分析。在选择使用什么方案来存储和分析之前，我们先来看看日志数据的特点。

- 日志是写入密集型的，超过 99% 的日志写入后不会被查询使用。
- 日志是标准的时间流数据，需要顺序写入，存储到数据库后，不会再进行修改变动。
- 日志是具有时效性的，一般只需要最近一段时间的日志来查询分析或者排查故障，一段时间以后会被保留策略清除或者归档。
- 日志是半结构化的，尽管我们将所有应用服务的日志都进行结构化，还是还包含系统日志，网络日志等日志，它们字段各不相同。
- 查询日志依赖全文检索和即席查询（Ad-hoc search）。
- 查询日志不要求日志具有强时效性，但是也无法接受按小时甚至按天的延时。

基于以上的存储和查询需求 [Elasticsearch](https://github.com/elastic/elasticsearch) 在日志存储领域一骑绝尘，Elasticsearch 可以使用时间范围作为索引，例如根据实际数据量以按日索引的话，由于能准确地预知明天、后天的日期，因此全部索引都可以预先创建，这免去了动态创建的寻找节点、创建分片、在集群中广播变动信息等开销。其次 Elasticsearch 可以针对不同时间范围的数据，进行冷热数据处理，针对最近一段时间的日志数据，在硬件中搭配 SSD 和更强的处理器，针对时间更久的数据，可以使用 HDD 存储或者 AWS S3 进行归档。

最重要的是 Elasticsearch 的核心存储引擎使用倒排索引天生适配全文检索，并且与其搭配的 Kibana(只负责图形界面和展示) 可以完美适配数据检索、聚合、统计和定制形成各种图形、表格。下图为 [Elastic Stack](https://www.elastic.co/elastic-stack/) 官方图片：

![nWzzSw](https://storage.guangzhengli.com/images/nWzzSw.jpg)

当然 Elasticsearch 也不是没有缺陷的，Mapping Explosion 就是 Elasticsearch 已知的缺陷之一，Elasticsearch 维护着一个文档 (document) 和字段 (fields) 的映射表，当这个映射表拥有太多的 key 时，会占用大量的内存，导致频繁的垃圾回收。还有它的次冷热分层存储也有其缺陷之处，Elasticsearch 每天都会将数据从热存储移动到冷存储，这也会影响集群的读写性能。

所以 Elasticsearch 对于一些中小型集群和全文检索有很好的支持，但是对于超大型集群和面向分析的场景，使用面向列的数据库 ClickHouse 可能会是一个更好的选择。日志的不可变性完美的适合 ClickHouse 的存储和分析能力，搭配 ClickHouse 压缩编解码器可以大幅度减少存储需求，其线性可扩展性可以轻松扩展集群。关于更多使用 ClickHouse 存储日志可以看这篇文章：[Log analytics using ClickHouse](https://blog.cloudflare.com/log-analytics-using-clickhouse/)。商业开源公司 [Signoz](https://signoz.io/blog/opentelemetry-logs/) 也将 ClickHouse 作为其 OpenTelemetry 实现方案。下图展示的即 Signoz 的[实时日志分析场景](https://www.splunk.com/en_us/blog/learn/splunk-log-observer.html)。

![8fYrZD](https://storage.guangzhengli.com/images/8fYrZD.jpg)

## 总结

回顾整篇文章，我们主要涵盖了打印日志的最佳实践，管理日志的难题和应对挑战的方案。日志作为系统服务最重要的观测手段之一，其重要性不言而喻。在系统日益复杂的今天，开发和运维人员应遵循良好的日志实践，以此来应对日志管理的挑战。

由于笔者能力有限，以上内容如果有任何错误或者片面的观点，还请直接指出。如果有补充的地方，欢迎在评论或者[仓库](https://github.com/guangzhengli/guangzhengli.github.io)进行补充，非常感谢！

## Reference

- https://blog.cloudflare.com/log-analytics-using-clickhouse/
- https://www.cortex.io/post/best-practices-for-logging-microservices
- https://stripe.com/blog/canonical-log-lines
- https://messagetemplates.org/
- https://www.dataset.com/blog/the-10-commandments-of-logging/
- https://brandur.org/canonical-log-lines
- https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/
- https://opentelemetry.io/docs/reference/specification/logs/#log-correlation
- https://signoz.io/blog/opentelemetry-logs/
- https://izsk.me/2021/10/31/OpenTelemetry-Trace/
- https://sematext.com/guides/kubernetes-logging/
- https://www.datadoghq.com/product/log-management/
- https://www.dynatrace.com/monitoring/platform/log-management-analytics/
- https://tech.meituan.com/2017/02/17/change-log-level.html
- http://icyfenix.cn/distribution/observability/logging.html



