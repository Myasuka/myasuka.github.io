+++
title = "Those Fading Stream Processing Engines"
date = "2025-09-14T06:48:00+08:00"
draft = false
+++

{{< callout type="info" emoji="ℹ️" >}}
  This article was originally published on [zhihu](https://zhuanlan.zhihu.com/p/611585539) @ 2023-03-06
{{< /callout >}}

{{< callout type="note" emoji="📝" >}}
  **Note**: This article was translated from Chinese. Some technical terms and concepts may differ from the original English terminology.
{{< /callout >}}

> A generation will eventually grow old, but there are always young people --- "The Train Drives Toward the Clouds, Dreams Rest Peacefully in the Ninth Heaven"

<!--more-->

I have been studying and working in the open-source big data ecosystem for ten years. Those big data systems that once appeared in textbooks have gradually become less active or even disappeared over time. This is actually a natural law - when new hardware and theoretical innovations emerge, they promote technological iteration. If the original systems cannot keep up with new opportunities or are no longer maintained and iterated, they will enter the final phase of a project's life cycle. In a way, this is similar to a person's life cycle, just as the song says: "A generation will eventually grow old, but there are always young people."

The reason for writing this article was the accidental discovery that Apache Heron (incubating)'s GitHub homepage was marked as archived on March 4th. In the field of stream processing, apart from the very popular Apache Flink in China and the early Apache Storm and Apache Spark Streaming that some people used, there were actually three other projects under the Apache Foundation: Apache Apex, Apache Samza, and Apache Heron. Among these projects, except for Flink and Spark (which focuses on batch processing), the others are no longer active or have entered the Apache Attic. The so-called Attic can be understood as putting projects on a shelf when they are no longer active.

![The four stream processing engines under the Apache Foundation mentioned in this article](/images/2025-09-14-14-47-40.png)

These stream processing engines were in a sense eliminated in competition with Flink. They definitely had certain shortcomings in their own functionalities or lacked the ability to attract more external users to promote community development. In this article, I will sort out these once-popular systems as a kind of elegy.

Since this article specifically discusses stream processing engines, the parts supporting batch processing are not discussed. If you are interested in stream-batch unified processing, you can refer to my previous article: "[It's Time to Bring Unified Stream-Batch Processing Engines to Mass Adoption](../stream-batch-engine-era)." In my opinion, for a stream processing engine to survive and thrive, it must possess the following key technical points:
1. Ability to achieve low latency under high throughput
2. Support for efficient stateful computing
3. Support for exactly-once processing semantics

The reason is that systems that only support simple real-time ETL, which means only map-like operations, are difficult to implement in large enterprises. For example, spark continuous streaming and Pulsar functions, these lightweight systems with light-weight titles, can also be achieved by general stream processing systems (such as Flink), so they are difficult to have advantages in the enterprise technology selection phase.

## Apache Storm
Open-sourced by Twitter in 2011, entered the Apache Foundation incubation in September 2013. It is a pioneer in stream processing engines that can achieve low-latency consumption, but does not support stateful computing or exactly-once semantics. From today's perspective, this project proved the possibility of low-latency stream processing, but lacked theoretical innovation. Without Google's dataflow paper support or Flink's lightweight checkpoint algorithm, its ceiling was not high. As an early real-time computing engine, it did not support stateful operations, so it provided the Trident API, which supported stateful operations and exactly-once semantics. However, the data processing mode regressed to micro-batch, and it only supported small-scale state.

By the way, JStorm was contributed to the Storm community by Alibaba in 2015, rewritten in Java with many additional features. Unfortunately, JStorm lost out to Flink in internal competition at Alibaba, so the code merging process was delayed until the Java-rewritten Storm 2.0.0 was officially released in 2019. More absurdly, JStorm's official website http://jstorm.io/ was hijacked by a pornographic website due to the domain not being renewed. This can be seen as a microcosm of early KPI-driven open-source projects in Chinese companies.

Since Flink emerged and was widely promoted, the activity level of the Storm project has obviously declined. In January 2023, discussions were initiated to move the project into the Attic for retirement, but it seems that there are still PMC members willing to continue maintaining it, so it has not been retired at this time.

## Apache Samza
Open-sourced by LinkedIn, entered the Apache Foundation incubation in July 2013. It was developed together with Kafka within LinkedIn, so the early committers of this project overlapped somewhat with the Kafka community. It was initially tightly coupled with Kafka, but has become more independent now. It seems that exactly-once semantics have never been supported (although there has been a discussion plan for many years: SEP-10 Exactly-once Processing in Samza). This project is in a somewhat delicate state. Although the project as a whole is no longer active, with versions released on average more than a year apart, it seems that it is still being used internally at LinkedIn (LinkedIn's Stream Processing Meetup with Apache Kafka, Samza, and Flink).

## Apache Apex
Backed by a startup company datatorrent, entered the Apache Foundation incubation in August 2015. Frankly speaking, this project did have some highlights - all three key technical points mentioned earlier were supported. Unfortunately, this project started too late, and the backing startup company closed in 2018, causing it to quickly lose core development contributors. The project also entered the Attic early in 2019, making it the earliest retired among the projects we reviewed. From the Apex project, we can also learn lessons: community growth requires involving more developers. Or in other words, even with good technology, academic or industrial backing is needed to make more users willing to try. Apex started later than Flink and did not leave any outstanding papers in academia, so it was naturally difficult to survive in industry.

## Apache Heron
Twitter's successor to Storm, entered the Apache Foundation incubation in 2017. This one started too late, and it seems that exactly-once semantics were not supported in the early days. The final result was that it couldn't even graduate from the incubation project and was directly retired in early 2023. Since it didn't officially graduate, it didn't even qualify to enter the Attic, making it the most pathetic among the projects reviewed in this article.

## Conclusion
It can be seen that competition in the computing engine field is actually more intense than in storage engines. Since users' migration costs are not as high as storage engines, it's easy to create a winner-takes-all situation. However, the ideas in these retired projects and their contributions to the entire industry are indelible and need to be learned and referenced by those of us still in the industry.

## References
https://medium.baqend.com/real-time-stream-processors-a-survey-and-decision-guidance-6d248f692056#.btmzcjgsn