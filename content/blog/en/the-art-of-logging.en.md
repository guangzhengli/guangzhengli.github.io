### Step 1: Direct Translation

---

title: The Art of Logging
date: 2023-02-28T20:33:36+08:00
tags: ["log", "developer"]
series: ["The art of code"]
featured: true
---

Understanding logs is not an easy task. Developers often struggle with whether the logs they print in their code are meaningful. SREs are often at a loss when facing production issues with insufficient logs. Ops teams often need to spend extra effort maintaining a large volume of logs, and project managers often do not want to invest too much manpower and financial resources in managing logs that have no actual business value.

Therefore, following good practices when developing applications and choosing mature solutions for collecting and managing logs can often alleviate these conflicts, which is the purpose of this article.

<!--more-->

## The Beginning of the Conflict

First, let's introduce why we need to record logs and their purpose. In fact, there is no need to introduce too much about the role of logs, as most developers have a clear understanding and strong need for logs when debugging code issues and solving bugs in different environments. Logs serve as debugging assistants and saviors in production environments. The author has only seen complaints about too few logs, almost never about too many logs from developers and operations personnel. Using logs to determine the code branch direction, whether the API request is correct, whether the core business data is correct, and whether there is error stack information are all primary means for developers and operations personnel to judge code and production issues. The author cannot imagine how to troubleshoot production bugs in a complex and large system without any logs.

If there is such a strong demand, why not log every line of code to record the context? This way, no matter what environment or code has issues, it can be traced through logs. Theoretically, this is feasible, but there are some problems that cannot be solved at present. One is the issue of log storage. Common medium and large systems have logs at the TB level, and super-large systems have logs at the PB level. According to [Cloudflare's data](https://blog.cloudflare.com/log-analytics-using-clickhouse/), it processes about 40 million requests per second, which is a huge challenge for storage costs. The second issue is the decline in search performance. Common log storage solutions like [Elasticsearch](https://github.com/elastic/elasticsearch) will see an explosive growth in the mapping relationships they maintain with massive logs. Even if different indexes are divided and different Elasticsearch clusters are managed in a distributed manner, it is difficult to ensure that search performance does not decline with the increase in data volume. The third issue is that generating massive logs during peak periods can slow down system performance and increase the risk of failures.

So, the simplest conclusion is that logs should not be printed too much, making storage and management difficult, nor should they be printed too little, making it impossible for operations personnel to troubleshoot issues. This sounds contradictory, but this is the art of logging!

## Log Levels

Before we plan to record logs, we need to determine the log level. Before introducing how to determine the log level, let's talk about the role of log levels. The first point is well known: log levels can help determine the priority of log information, effectively reducing information noise and alert fatigue. The second point is that when querying logs, filtering by log level can effectively find the required logs.

Common log levels are as follows, in ascending order: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`. Usually, in a local environment, we configure the log level to `Trace`, which prints all levels of logs. However, in a production environment, due to storage pressure, query performance, and disk IO impact, the log level is adjusted to `INFO` or even `ERROR`. This means that logs below this level in the code will not be printed.

Does this mean that we only need to print logs that match the production environment log level in the code? For example, if the production environment only prints `INFO` level logs, is it unnecessary to write `DEBUG` level logs in the code since they won't be printed? Some project managers, to save on log management costs, only print `ERROR` level logs in the production environment. Does this mean that `INFO` level logs can also be omitted? Printing only logs that match the production environment log level is indeed the current situation in most projects, but reality is often more complex, as shown in the following scenarios.

### Scenario One

An engineer investigating the low performance of a resource creation API in the production environment found that the API was logging the resource object at the `INFO` level before saving it. Due to the many attributes of the resource object, a large number of logs were printed during peak business periods, exhausting the buffer memory and slowing down the main thread, causing overall service performance to decline. Therefore, the engineer deleted the log operation to reduce disk IO consumption in the production environment and solve the performance issue.

However, one day, due to a modification in the service code on the API service call chain, the created object had errors. Without the logs of saving the resource in the production environment, it was impossible to determine whether the API request parameters were problematic or if there was an issue with the subsequent calculation logic. Should we modify the log level and rebuild and deploy the service?

### Scenario Two

Suppose the log level in the production environment is set to `ERROR`. At a certain moment, a downstream service failure causes a large number of request timeouts. During peak business periods with very high QPS, a large number of error logs are generated in a short time, significantly increasing disk IO and consuming a lot of CPU, leading to a service crash. How should we handle this?

### Scenario Three

An engineer troubleshooting a production issue finds that `INFO` level logs are insufficient to determine the root cause. A `DEBUG` level log is needed, but the production environment only has `INFO` level logs. Should we modify the log level and restart the service?

### Log Level Specification and Dynamic Adjustment

To solve the above problems, we need to clarify the log level specifications in the project and avoid using log levels arbitrarily for debugging convenience and storage reduction. Additionally, we need to add a **dynamic adjustment** feature to log levels. This means lowering the log output level online to obtain comprehensive debug logs when solving online issues, helping engineers locate problems more efficiently. When massive logs slow down service performance in the production environment, raise the log output level to reduce log generation, alleviate disk IO pressure, and improve service performance.

Here are some suggestions for log levels:

- TRACE: Use it during development to trace errors, but never commit it to the version control system (VCS).
- DEBUG: Record everything happening in the program. Mainly used during debugging. It is recommended to reduce the number of debug statements before entering the production stage, leaving only the most meaningful entries, which can be activated during troubleshooting.
- INFO: Record all user-driven events or system-specific operations (e.g., periodic scheduled operations).
- WARN: Record all events that may become errors. For example, if a database call takes longer than a predefined time or if the memory cache is nearing capacity. This allows for appropriate automatic alerts and better understanding of system behavior before a failure during troubleshooting.
- ERROR: Record every error condition at this level. This can be an API call returning an error or an internal error condition.
- FATAL: Indicates that the entire service is no longer working. Use this level very sparingly. Usually, this level indicates the end of the program.

## Recording Logs

### When to Record Logs

There is no standard specification for when to record logs. Developers need to judge based on the business and code. In addition to the usual recording of events, such as what operations were performed, unexpected situations, unhandled exceptions or warnings during runtime, and periodic automatic tasks, the author also suggests adding logs in the following scenarios:

- When calling third-party systems, record the API URL, `Request/Response Body`, and exceptions in the logs. This helps provide clear and detailed log reports to explain the cause of failures, reducing the need for different system service operations personnel or different companies to define responsibilities and promoting problem resolution more smoothly.
- Add logs to key code and branches of important core businesses, such as if-then-else statements. This helps developers understand whether the program traversed the expected path based on its current state. Since core business data is generally difficult to reproduce manually, understanding the code branch direction is crucial.
- Audit logs for core businesses. If a business is related to legal or contractual matters, adding audit logs to the corresponding operations is necessary. The storage of logs requires a strongly consistent database.
- Output configuration information when the application service starts. The initialization configuration logic is usually executed only once and is not easy to reproduce during diagnosis, so it should be output to the logs.

### Log Attributes

In addition to the usual `log level`, `timestamp`, `message`, `exception`, and `stack trace` that need to be printed in logs, other fields are often needed to help locate and find the root cause. Common additional fields include:

- `trace id`: The unique ID for service link tracking. When a request enters the system's 7-layer gateway, add the unique `trace id` for the entire lifecycle of the request in the HTTP header and carry it along with the request. When the request link is long and developers find it difficult to locate the complete request log, the `trace id` helps reverse lookup the complete log.
- `span id`: Represents a single operation split from the `trace id` lifecycle. For example, when a request reaches each service, the service generates a `span id` for the request. The first `span id` is called the root span, and the `span id` passed from the upstream with the request is recorded as `pspanid` (parent-spanid). When the current service generates a `span id` and passes it to the downstream service, this `span id` is recorded as `pspanid` by the downstream service. This helps restore the entire request call chain view when service calls are complex.
- `user id`: The unique ID of the user. Ensure that when users report issues or submit tickets, the corresponding error logs can be directly queried based on the user's unique ID, reducing interference items and shortening the troubleshooting cycle.
- `tenant_id`: The tenant ID (if any). Very helpful for multi-tenant systems.
- `request uri`: The current microservice request URI (a user's business operation may correspond to multiple services with different `request uri`). When a business issue occurs, the `request uri` corresponding to the business **entry** can quickly obtain the `trace id`, and then use the `trace id` to find the corresponding request logs to quickly solve the problem.
- `application name`: The name of the current microservice. Helps identify which service generated the log and filter logs by `application name` to query overall service failures.
- `pod name`: The name of the k8s resource Pod where the current request is located (if any). Most microservices currently use k8s for container orchestration. Printing the `pod name` helps restart or kill the Pod when a `pod` fails.
- `host name`: The name of the machine where the current request is located. Even if microservices are hosted by k8s, there may be cases where a machine in the k8s cluster has disk or network failures, causing the service to not work properly. Printing the `host name` helps troubleshoot the last mile of the issue, i.e., failures caused by hardware problems.

### Log Security

Logs need to ensure the security of the logging framework and the handling of sensitive information. Framework security means using mature, production-verified logging framework libraries rather than creating your own. Handling sensitive information is the lifeline of most companies. Please remember the security and compliance requirements of logs:

- Do not leak sensitive personal identifiable information (PII).
- Do not leak encryption keys or secrets.
- Ensure the company's privacy policy covers log data.
- Ensure log providers meet compliance requirements.
- Ensure data storage time requirements are met.

### Bad Log Practices

- Using non-English characters to print logs.
  - English logs are recorded in ASCII characters. This is especially important because non-English characters may not be correctly displayed due to character set or encoding issues.
  - English logs have built-in word segmentation effects. When using inverted index storage engines like `ElasticSearch` to store logs, non-English logs require special word segmentation tools, and the storage and query effects are not as good as English.
- Logs without context. Similar to directly printing `Transaction failed` or `User operation succeeds`. When writing code, the log message can be understood through the code context, but when reading the log itself, this context does not exist, and these messages may be incomprehensible.
- Placing log printing operations in loops. Unless there is a specific need, the printed logs are not only difficult to read and search but also consume a lot of storage resources.
- Referencing slow operation data. If the data needed for printing logs is not available in the current context and requires calling remote services, fetching from the database, or performing extensive calculations, consider whether this information is necessary and appropriate to include in the logs.

## Log Observability

In the past decade, the rise of microservices and cloud-native technologies has brought significant changes to the field of log collection, storage, and analysis. In the early days, we did not need to collect logs. At that time, all logs of monolithic services were stored in files, and tools like tail, grep, and awk were used to extract information from the logs. However, in today's increasingly complex systems, this approach can no longer meet our needs. To cope with the increasingly complex log management requirements, the open-source community and industry have developed a series of solutions, such as the most popular [Elastic Stack](https://www.elastic.co/elastic-stack/) open-source solution, and one-stop solutions provided by cloud vendors like [AWS DataDog](https://www.datadoghq.com/product/log-management/) and [Azure Dynatrace](https://www.dynatrace.com/monitoring/platform/log-management-analytics/).

Regardless of the solution used, log management is no longer a simple topic. Between the conscious act of printing logs and querying and analyzing logs, there are several steps involved in collecting, buffering, aggregating, processing, indexing, and storing logs, each of which contains its own challenges.

![azaofU](https://storage.guangzhengli.com/images/azaofU.jpg)

### Log Collection

Initially, we used [Logstash](https://www.elastic.co/logstash/) from the [Elastic Stack](https://www.elastic.co/elastic-stack/) to collect and process logs. Different services in the system actively sent requests to push logs to Logstash using tcp/udp protocols. Logstash then transformed and processed the logs (data structuring) and output them. This mode lasted for a long time, but it also had serious drawbacks. Logstash and its plugins are written in JRuby and run on a separate Java virtual machine process, with a default heap size of 1GB. If thousands of log collectors need to be deployed, this solution becomes too heavy. Therefore, Elastic.co later rewrote a lighter and more efficient log collector [Filebeat](https://github.com/elastic/beats/tree/master/filebeat) in Golang, which alleviated this contradiction.

In addition, [Fluentd](https://github.com/fluent/fluentd) is usually the preferred open-source log collector when working with Kubernetes. It is Kubernetes-native and can be deployed seamlessly with Kubernetes using DaemonSet. It allows collecting logs from different sources like Kubernetes clusters, MySQL, Apache2, etc., and parsing and sending them to desired destinations like Elasticsearch, Amazon S3, etc. Fluentd is written in Ruby and performs well under low capacity, but it may have performance issues when the number of nodes and applications increases.

Finally, during log collection, massive logs generated during peak business periods can affect service stability and cause log loss. In such cases, we need to add a buffering layer before Logstash or the log storage database. In smaller systems, [Redis streams](https://redis.io/topics/streams-intro/) is a good choice. For larger-scale data, a Kafka cluster or message queue solutions provided by cloud vendors are the best options.

### Structured Logs

After collecting logs, we need to structure them. Logs are unstructured data, and a single log line usually contains multiple pieces of information. Without processing, logs can only be used in their raw form for full-text search, which is not conducive to statistics, comparison, or conditional filtering. For example, the following line is an Nginx server access log.

```
10.209.21.28 - - [04/Mar/2023:18:12:11 +0800] "GET /index.html HTTP/1.1" 200 1314 "https://guangzhengli.com" Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
```

Although we are used to the default Nginx format, the above example is still difficult to read and process. We can use Logstash or other tools to convert it into structured data, such as JSON format.

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

After structuring, inverted index databases like Elasticsearch can create indexes for different data items, perform query statistics, aggregation, and other operations.

In addition, there is an industrial practice recommended by [Splunk](https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/) to put fields in key-value pairs in a single standardized log line (logfmt). For example, the Nginx log as a standardized log line would look like this:

``` 
remote-ip=10.209.21.28 remote-user=null datetime="04/Mar/2023:10:49:21 +0800" method=GET url=/index.html protocol=HTTP/1.1 status=200 size=1314 refer=https://guangzhengli.com agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"
```

After storing this data in Splunk, it can be retrieved using the built-in query language. For example, using `method=get status=500` to query all GET methods that returned a 500 response. Using `method=get status=500 earliest=-7d | timechart count` to get the total number and chart of GET methods that returned a 500 response in the past 7 days. This method is not used much by the author, so those interested can refer to the article [Stripe canonical-log-lines](