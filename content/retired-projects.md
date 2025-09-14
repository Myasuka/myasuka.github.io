+++
title = "那些逐渐消亡的流式计算引擎"
date = "2025-09-14T06:48:00+08:00"
draft = false
+++

{{< callout type="info" emoji="ℹ️" >}}
  本文原先发表于 [zhihu](https://zhuanlan.zhihu.com/p/611585539) @ 2023-03-06
{{< /callout >}}



> 一代人终将老去，但总有人正年轻 --- 《火车驶向云外，梦安魂于九霄》

<!--more-->

进入开源大数据生态学习和工作十年了，那些曾经出现在教材里面的大数据系统，随着时间的流逝已经逐渐不再活跃，甚至消亡了。这其实也是自然规律，新的硬件、新的理论革新出现，就会促进技术的迭代，如果原来的系统没有更上新的机会，或者不再有人维护和迭代，其就会进入一个项目生命的尾期。某种意义上来说，这其实与一个人的生命周期是类似的，就像歌中所唱：一代人终将老去，但总有人正年轻。
写这篇的文章的起因是偶然发现Apache Heron (incubating)的github主页在3月4日被标记为归档了，在流式计算领域，实际上除了国内很火的Apache Flink，以及早期有人在使用的Apache Storem和Apache Spark streaming。其实还有另外三个Apache基金会下的项目，分别是Apache Apex，Apache Samza，Apache Heron。这些项目中，除了Flink和专攻批计算的Spark外，其他的项目均已经不活跃甚至进入了Apache Attic，所谓Attic，意思为阁楼，可以理解成项目不再活跃之后而束之高阁吧。

![本文提及的四个Apache基金会下的流式计算引擎](images/2025-09-14-14-47-40.png)

这些流式计算引擎在与Flink的竞争中某种意义上淘汰下来，在自身的功能上肯定是存在一定的不足，或者缺乏吸引更多外部用户进来一起推动社区的发展。我在本文对这些曾经流行过的系统做一些梳理，也算是一种挽歌。

由于本文专门讨论流式计算引擎，所以关于其支持批计算的部分不在讨论中，如果对流批一体感兴趣，可以参阅我之前的文章：《[是时候让流批一体的计算引擎大规模落地了](../stream-batch-engine-era)》。在我看来，对于一个流式计算引擎，支撑其可以存活下来并且可以活得很好，必须具备以下几个关键技术点：
1. 可以实现高吞吐下的低延迟
2. 支持高效的stateful计算
3. 支持exactly-once的处理语义

原因在于，仅仅支持简单的实时ETL，也就是仅有map类操作的系统，在大型企业中是很难落地的。例如spark continous streaming，Pulsar functions，这些打着轻量级title的系统的功能，同样也可以由通用流式计算系统达成（例如Flink），所以在企业的技术选型阶段是很难有优势的。

## [Apache Storm](https://storm.apache.org/)
2011年就由Twitter开源，2013年9月进入Apache基金会孵化，是流式计算引擎的先驱，可以实现低延迟消费，但是并不支持stateful计算以及exactly-once的语义。从现在的眼光看，这个项目证明了低延迟流式计算的可能性，但是缺乏理论革新，没有google的dataflow论文加持，也没有类似Flink的轻量级checkpoint算法，导致其上限不高。作为早期的实时计算引擎，还不支持stateful操作，所以又提供了Trident的API，支持了stateful操作和exactly-once的语义，但是数据处理模式又回退成了微批（micro-batch），并且只支持小规模状态。
顺带一提JStorm，这个项目最早是2015年时候由阿里巴巴向Storm社区贡献，由Java重写了Storm，并且增加了很多功能，可惜阿里内部JStorm在与Flink的竞争中落败，相关代码的合并进程也就一直拖延了，直到2019年，完全由java重写的Storm-2.0.0才正式发布。更荒诞的是JStorm的官网 http://jstorm.io/ 由于域名没有续费，而被抢注成了一个情色网站。可以说是国内公司早期KPI式开源的一个缩影。
自从Flink横空出世，并大规模推广开后，Storm这个项目的活跃度就明显下降了。2023年1月，发起了将项目进入Attic进行退役的讨论，不过目前看来社区还是有PMC愿意继续take，使其不在此刻就退役。

## [Apache Samza](https://samza.apache.org/)
由Linkedin开源，2013年7月进入Apache基金会孵化，在Linkedin内部与Kafka一起研发，所以这个项目的初期commiter与Kafka社区有一定重合。早期与Kafka耦合比较严重，但是目前已经比较独立，似乎一直都没有支持exactly-once语义（虽然有一个存在了很多年的讨论计划：SEP-10 Exactly-once Processing in Samza）。这个项目处于一种有些微妙的状态，虽然项目整体已经不再活跃，平均一年多才有一个版本发布，但是似乎目前在Linkedin内部还有使用（Linkedin 的Stream Processing Meetup with Apache Kafka, Samza, and Flink）。

## [Apache Apex](https://apex.apache.org/)
背后是一家创业公司datatorrent，2015年8月进入Apache基金会孵化。坦率地说，这个项目还是有一些亮点的，前面提到的三个关键技术点均支持，但是很可惜这个项目发力晚了点，而且背后的创业公司在2018年关闭了，导致其很快就失去了核心开发贡献人员，项目也在2019年早早地进入了Attic，是我们这批回顾的项目中最早退役的。从Apex这个项目身上，我们也能学到经验教训：一个社区的成长是需要involve更多的开发者，或者说即使有不错的技术，也需要在学术界或者工业界的背书，才能让更多的用户愿意尝试。Apex相比于Flink起步晚了些，也没有在学术界留下什么卓然的论文，自然很难最终在工业界存活下来。

## [Apache Heron](https://github.com/apache/incubator-heron)
Twitter开源的Storm继承者，2017年才进入Apache基金会孵化，这位属于发力太晚了，而且似乎早期还不支持exactly once语义，最终结果连从孵化项目毕业都没完成，就直接在2023年初退役了。由于没有正式毕业，所以连进入Attic的资格都没有，可以说是本文回顾的项目中最凄惨的。
结语
可以看出，其实计算引擎领域的竞争会相比于存储引擎上更为激烈，由于用户的迁移成本没有存储引擎那么大，所以很容易产生赢家通吃的局面。不过这些退役的项目中的想法以及对整个行业的贡献，是不可磨灭的，也是我们这些还在行业中的人需要学习和借鉴的。


参考文章
https://medium.baqend.com/real-time-stream-processors-a-survey-and-decision-guidance-6d248f692056#.btmzcjgsn 

