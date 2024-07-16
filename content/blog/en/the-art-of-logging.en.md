### Step 1: Direct Translation

---

title: The Art of Logging
date: 2023-02-28T20:33:36+08:00
tags: ["log", "developer"]
series: ["The art of code"]
featured: true
---

Understanding logs is not an easy task. Developers often struggle with whether the logs they print in their code are meaningful. SREs are often at a loss when facing production issues with insufficient logs. Ops teams need to spend extra effort maintaining a vast amount of logs, and project managers often do not want to invest too much manpower and financial resources in managing logs that have no actual business value.

Therefore, following good practices when developing applications and choosing mature solutions for collecting and managing logs can often alleviate these conflicts, which is the purpose of this article.

<!--more-->

## The Beginning of the Conflict

First, let's introduce why we need to record logs and their purpose. In fact, the role of logs does not need much introduction because most developers have a clear understanding and strong need for logs when debugging code issues and solving bugs in different environments. Logs serve as debugging assistants and saviors in production environments. The author has only seen complaints about too few logs, almost never about too many logs from developers and operations personnel. Using logs to determine the code branch direction, whether the API request is correct, whether the core business data is correct, and whether there is error stack information are all primary means for developers and operations personnel to judge code and production issues. The author cannot imagine how to troubleshoot production bugs in a complex and large system without any logs.

If there is such a strong demand, why not log every line of code to record the context? This way, no matter what environment or code has issues, it can be traced through logs. Theoretically, this is feasible, but there are some problems that cannot be solved at present. One is the issue of log storage. Common medium and large systems have logs at the TB level, and super-large systems have logs at the PB level. According to [Cloudflare's data](https://blog.cloudflare.com/log-analytics-using-clickhouse/), it processes about 40 million requests per second, which is a huge challenge for storage costs. The second is the decline in search performance. Common log storage solutions like [Elasticsearch](https://github.com/elastic/elasticsearch) will see an explosive growth in the mapping relationships they maintain with massive logs. Even if different indexes are divided and different Elasticsearch clusters are managed in a distributed manner, it is difficult to ensure that search performance does not decline as the data volume increases. The third is that the generation of massive logs during peak periods will slow down system performance and increase the risk of failures.

So, the simplest conclusion is that logs should not be printed too much, making storage and management difficult, nor should they be printed too little, making it impossible for operations personnel to troubleshoot issues. This sounds contradictory, but this is the art of logging!

## Log Levels

Before we plan to record logs, we need to determine the log level. Before introducing how to determine the log level, let's talk about the role of log levels. The first point is well known: log levels can help determine the priority of log information, effectively reducing information noise and alert fatigue. The second is that when querying logs, filtering by log level can effectively find the required logs.

Common log levels are as follows, in ascending order: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`. Usually, in the local environment, we configure the log level to `Trace`, which prints all levels of logs. However, in the production environment, due to storage pressure, query performance, and disk IO impact, the log level is adjusted to `INFO` or even `ERROR`. This means that logs below this level in the code will not be printed.

Does this mean that we only need to print logs that match the production environment log level in the code? For example, if the production environment only prints `INFO` level logs, is it unnecessary to write `DEBUG` level logs in the code since they will not be printed? Some project managers, to save log management costs, only print `ERROR` level logs in the production environment. Does this mean that `INFO` level logs can also be omitted? Printing only logs that match the production environment log level is indeed the current situation in most projects, but reality is often more complex, as in the following scenarios.

### Scenario One

An engineer investigating the low performance of a resource creation API in the production environment found that the API was writing `INFO` level logs before saving the resource. Due to the many attributes of the resource object, a large number of logs were printed during business peak periods, exhausting the buffer memory and slowing down the main thread, causing overall service performance to decline. Therefore, the engineer deleted the log printing operation to reduce production environment disk IO consumption and solve the performance issue.

However, one day, due to a modification in the service code on the API service call chain, the created object had errors. Without the logs of saving the resource in the production environment, it was impossible to determine whether the API request parameters were problematic or if there were issues in the subsequent calculation logic. Should we modify the log level and redeploy the service?

### Scenario Two

Suppose the production environment log level is set to `ERROR`. At a certain moment, a downstream service failure caused a large number of request timeouts. During business peak periods with very high QPS, a large number of error logs were generated in a short time, significantly increasing disk IO and consuming a lot of CPU, leading to a service crash. How should we handle this?

### Scenario Three

An engineer troubleshooting a production issue found that `INFO` level logs were insufficient to identify the root cause. A `DEBUG` level log was needed, but the production environment only had `INFO` level logs. Should we modify the log level and restart the service?

### Log Level Specification and Dynamic Adjustment

To solve the above problems, we need to clarify the log level specifications in the project, not using log levels arbitrarily for debugging convenience and reducing storage. Second, we need to add **dynamic adjustment** functionality to log levels. This means lowering the online log output level to obtain comprehensive debug logs when solving online issues, helping engineers improve problem localization efficiency. When the production logs increase massively and slow down service performance, raise the online log output level to reduce log generation, alleviate disk IO pressure, and improve service performance.

Here are some suggestions for log levels:

- TRACE: Should be used during development to trace errors but never committed to the version control system (VCS).
- DEBUG: Record everything happening in the program. Mainly used during debugging. It is recommended to reduce the number of debug statements before entering the production stage, leaving only the most meaningful entries, and can be activated during troubleshooting.
- INFO: Record all user-driven events or system-specific operations (e.g., regularly scheduled operations).
- WARN: Record all events that may become errors. For example, if a database call takes longer than a predefined time or if the memory cache is nearing capacity. This allows for appropriate automatic alerts and better understanding of system behavior before a failure during troubleshooting.
- ERROR: Record every error condition at this level. This can be an API call returning an error or an internal error condition.
- FATAL: Indicates that the entire service is no longer working. Use this level very sparingly. Usually, this level records the end of the program.

## Recording Logs

### When to Record Logs

There is no standard specification for when to record logs. Developers need to judge based on business and code. In addition to regular event recording, such as what operations were performed, unexpected situations, unhandled exceptions or warnings during runtime, and regularly automated tasks, the author also suggests adding logs in the following scenarios:

- When calling third-party systems, record the API URL, `Request/Response Body`, and exceptions in the logs. This helps provide clear and explicit log reports to explain the cause of failures, reducing the **responsibility demarcation** between different system service operations personnel or different companies, and promoting problem resolution more smoothly.
- Add logs to key code and branches of important core businesses, such as if-then-else statements. This helps developers understand whether the program traversed the expected path based on its current state. Since core business data is generally difficult to reproduce manually, understanding the code branch direction is crucial.
- Audit logs for core business. If a business is related to law or contracts, adding audit logs to corresponding operations is necessary. The storage logs require a strongly consistent database.
- Output configuration information when the application service starts. The initialization configuration logic usually only executes once and is not easy to reproduce during diagnosis, so it should be output to the logs.

### Log Attributes

In addition to the conventional `log level`, `timestamp`, `message`, `exception`, and `stack trace` that need to be printed in logs, other fields are often needed to help locate and find the root cause. Common additional fields include:

- `trace id`: The unique ID for service link tracking. When a request enters the system's 7-layer gateway, add the unique `trace id` for the entire lifecycle of the request in the HTTP header and carry it along with the request. When the request link is long and developers find it difficult to locate the complete request log, `trace id` helps reverse lookup the complete log.
- `span id`: Represents a single operation split from the `trace id` lifecycle. For example, when a request reaches each service, the service generates a spanid for the request. The first spanid is called the root span, and the spanid passed from the upstream with the request is recorded as pspanid (parent-spanid). The spanid generated by the current service is passed to the downstream service with the request, and this spanid is recorded as pspanid by the downstream service. Thus, `span id` helps restore the entire request call chain view when service calls are complex.
- `user id`: The unique ID of the user. Ensure that when users report issues or submit tickets, the corresponding error logs can be directly queried based on the user's unique ID, reducing interference items and shortening the troubleshooting cycle.
- `tenant_id`: The tenant ID (if any). Very helpful for multi-tenant systems.
- `request uri`: The current microservice request URI (a user's business operation may correspond to multiple services with different `request uri`). When a business issue occurs, the `request uri` corresponding to the business **entry** often quickly obtains the `trace id`, and then the corresponding request log can be quickly found through the `trace id`.
- `application name`: The name of the current microservice. Helps identify which service generated the log and filter logs by `application name` to query overall service failures.
- `pod name`: The name of the k8s resource Pod where the current request is located (if any). Most microservices currently use k8s for container orchestration. Printing the `pod name` helps restart or kill the Pod when a `pod` fails.
- `host name`: The name of the machine where the current request is located. Even if microservices are hosted by k8s, there may be cases where a machine in the k8s cluster has disk or network failures, causing the service to malfunction. Printing the `host name` helps troubleshoot the last mile of the issue, i.e., failures caused by hardware problems.

### Log Security

Logs need to ensure the security of the logging framework and the handling of sensitive information. Framework security means using mature, production-verified logging framework libraries rather than reinventing the wheel. Handling sensitive information is the lifeline of most companies. Please remember the security and compliance requirements of logs:

- Do not leak sensitive personal identifiable information (PII).
- Do not leak encryption keys or secrets.
- Ensure the company's privacy policy covers log data.
- Ensure log providers meet compliance requirements.
- Ensure data storage time requirements are met.

### Bad Smells in Logs

- Using non-English characters to print logs.
  - English logs will be recorded in ASCII characters. This is particularly important because characters like Chinese may not be correctly presented after a series of processing due to character set or encoding issues.
  - English has built-in word segmentation effects. When using inverted index storage engines like `ElasticSearch` to store logs, Chinese logs not only require adding special word segmenters, but the storage and query effects are also not as good as English.
- Logs without context. Similar to directly printing `Transaction failed` or `User operation succeeds`. When writing code, the log message can be understood through the code context, but when reading the log itself, this context does not exist, and these messages may be incomprehensible.
- Placing log printing operations in loops. Unless there is a specific need, the printed logs are not only difficult to read and search but also consume a lot of storage resources.
- Referencing slow operation data. If the data needed for printing logs is not available in the current context and requires calling remote services, fetching from the database, or performing extensive calculations, consider whether this information is necessary and appropriate to include in the logs.

## Log Observability

In the past decade, significant changes have occurred in the field of log collection, storage, and analysis due to the rise of microservices and cloud-native technologies. In the early days, we did not need to collect logs. At that time, all logs of monolithic services were stored in files, and tools like tail, grep, and awk were used to extract information from the logs. However, in today's increasingly complex systems, this approach can no longer meet our needs. To cope with the increasingly complex log management requirements, the open-source community and industry have developed a series of solutions, such as the most popular [Elastic Stack](https://www.elastic.co/elastic-stack/) open-source solution, and one-stop solutions provided by cloud vendors like [AWS DataDog](https://www.datadoghq.com/product/log-management/) and [Azure Dynatrace](https://www.dynatrace.com/monitoring/platform/log-management-analytics/).

Regardless of the solution used, log management is no longer a simple topic. Between the conscious act of printing logs and querying and analyzing logs, there are several steps involved in collecting, buffering, aggregating, processing, indexing, and storing logs, each of which contains its own challenges.

![azaofU](https://storage.guangzhengli.com/images/azaofU.jpg)

### Log Collection

Initially, we used [Logstash](https://www.elastic.co/logstash/) from the [Elastic Stack](https://www.elastic.co/elastic-stack/) to collect and process logs. Different services in the system actively sent requests to push logs to Logstash using tcp/udp protocols. Logstash then transformed and processed the logs (data structuring) and output them. This mode lasted for a long time, but it also had serious drawbacks. Logstash and its plugins are written in JRuby and run on a separate Java virtual machine process, with a default heap size of 1GB. If thousands of log collectors need to be deployed, this solution becomes too heavy. Therefore, Elastic.co later rewrote a lighter and more efficient log collector [Filebeat](https://github.com/elastic/beats/tree/master/filebeat) in Golang, which alleviated this contradiction.

In addition, [Fluentd](https://github.com/fluent/fluentd) is usually the preferred open-source log collector when working with Kubernetes. It is Kubernetes-native and can be deployed seamlessly with Kubernetes using DaemonSet. It allows collecting logs from different sources like Kubernetes clusters, MySQL, Apache2, etc., and parsing and sending them to desired locations like Elasticsearch, Amazon S3, etc. Fluentd is written in Ruby and performs well under low capacity, but when the number of nodes and applications needs to be increased, it may also have performance issues.

Finally, during log collection, massive logs generated during business peak periods may affect service stability and cause log loss. In such cases, a buffering layer needs to be added before Logstash or the log storage database. In smaller systems, [Redis streams](https://redis.io/topics/streams-intro/) is a good choice. For larger-scale data, a Kafka cluster or message queue solutions provided by cloud vendors are the best options.

### Structured Logs

After collecting logs, we need to process them into a structured format. Logs are unstructured data, and a single log line usually contains multiple pieces of information. Without processing, logs can only be used in their raw form for full-text search, which is not conducive to statistical comparison or conditional filtering. For example, the following line is an Nginx server access log.

```
10.209.21.28 - - [04/Mar/2023:18:12:11 +0800] "GET /index.html HTTP/1.1" 200 1314 "https://guangzhengli.com" Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
```

Although we are used to the default Nginx format, the above example is still difficult to read and process. We can use Logstash or other tools to convert it into a structured format, such as JSON.

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

In addition, there is an industrial practice like [Splunk](https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/) that recommends converting fields into key-value pairs in a single standardized log line (logfmt). For example, converting Nginx logs into standardized log lines would look like this:

``` 
remote-ip=10.209.21.28 remote-user=null datetime="04/Mar/2023:10:49:21 +0800" method=GET url=/index.html protocol=HTTP/1.1 status=200 size=1314 refer=https://guangzhengli.com agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"
```

After storing this data in Splunk, it can be queried using the built-in query language. For example, using `method=get status=500` to query all GET methods that returned a 500 response. Using `method=get status=500 earliest=-7d | timechart count` to get the total number and chart of GET methods that returned a 500 response in the past 7 days. This method is not used much by the author, so those interested can refer to the article [Stripe canonical-log-lines](https://stripe.com/blog/canonical-log-lines) for more details.

###