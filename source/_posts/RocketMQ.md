---
title: RocketMQ
date: 2022-08-15 22:21:00
tags: MessageQueue
categories: 学习笔记
index_img: /img/rocketmq_logo.jpg
---

# RocketMQ

消息队列

常见的消息中间件：

Kafka, ActiveMQ, RabbitMQ, RocketMQ



# 第1章 RocketMQ概述

## 一、MQ概述

### 1、MQ简介

MQ，Message Queue，是一种提供消息队列服务的中间件，是一套提供了消息生产、存储、消费过程API的软件系统。

### 2、MQ用途

**限流削峰**

MQ可以将系统的超量请求暂存其中，以便系统后期可以慢慢进行处理，从而避免了请求的丢失或系统被压垮。

**异步解耦**

上游系统对下游系统的调用若为同步调用，则会大大降低系统的吞吐量与并发度，且系统的耦合度太高。而异步调用则会解决这些问题。所以两层之间若要实现由同步到异步的转化，一般性的做法就是在这两层间添加一个MQ层。

**数据收集**

分布式系统会产生海量级数据流，如：业务日志、监控数据、用户行为等。针对这些数据流进行实时或批量采集汇总，然后对这些数据流进行大数据分析，这是当前互联网平台的必备技术。通过MQ完成此类数据收集是最好的选择。

### 3、常见MQ产品

**ActiveMQ**

ActiveMQ是使用Java语言开发的一款MQ产品。早期很多公司与项目中都在使用。但现在的社区活跃度已经很低。现在的项目中已经很少使用了。

**RabbitMQ**

RabbitMQ是使用ErLang语言开发的一款MQ产品。其吞吐量较Kafka与RocketMQ要低，且由于其不是Java语言开发，所以企业内部对其实现定制化开发难度较大。

**Kafka**

Kafka是使用Scala/Java语言开发的一款MQ产品。其最大的特点就是高吞吐量，常用于大数据领域的实时计算、日志采集等场景。其没有遵循任何常见的MQ协议，而是使用自研协议。

**RocketMQ**

RocketMQ是使用Java语言开发的一款MQ产品。经过数年阿里双11的考验，性能与稳定性非常高。其没有遵循任何常见的MQ协议，而是使用自研协议。对于Spring Cloud Alibaba，其支持RabbitMQ、Kafka，但提倡使用RocketMQ。

**对比**

| 关键词     | ActiveMQ | RabbitMQ | Kafka                       | RocketMQ                    |
| ---------- | -------- | -------- | --------------------------- | --------------------------- |
| 开发语言   | Java     | Erlang   | Java                        | Java                        |
| 单机吞吐量 | 万级     | 万级     | 十万级                      | 十万级                      |
| Topic      | -        | -        | 百级Topic时会影响系统吞吐量 | 千级Topic时会影响系统吞吐量 |
| 社区活跃度 | 低       | 高       | 高                          | 高                          |

### 4、MQ常见协议

一般情况下MQ的实现要遵循一些常规性协议。常见的协议如下：

**JMS**

JMS，Java Messaging Service ，是Java平台上有关MOM（Message Oriented Middleware，面向消息的中间件）的技术规范，它便于消息系统中的Java应用程序进行消息交换，并且通过提供标准的产生、发送、接收消息的接口，简化企业应用的开发。ActiveMQ是该协议的典型实现。

**STOMP**

STOMP，Streaming Text Orientated Message Protocol，是一种MOM设计的简单文本协议。STOMP提供一个可互操作的连接格式，允许客户端与任意STOMP消息代理进行交互。ActiveMQ是该协议的典型实现，RabbitMQ通过插件可以支持该协议。

**AMQP**

AMQP，Advanced Message Queuing Protocol，一个提供统一消息服务的应用层标准，是应用层协议的一个开放标准，是一种MOM设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言等条件的限制。RabbitMQ是该协议的典型表现。

**MQTT**

MQTT，Message Queuing Telemetry Transport，是IBM开发的一个即时通讯协议，是一种二进制协议，主要用于服务器和低功耗IoT（物联网）设备间的通信。该协议支持所有平台，几乎可以把所有联网物品和外部连接起来，被用来当作传感器和致动器的通信协议。RabbitMQ通过插件可以支持该协议。

## 二、RocketMQ概述

### 1、RocketMQ简介

RocketMQ是一个统一消息引擎、轻量级数据处理平台。

RocketMQ是一款阿里巴巴开源的消息中间件。2016年11月28日，阿里巴巴向Apache软件基金会捐赠RocketMQ，成为Apache孵化项目。2017年9月25日，Apache宣布RocketMQ孵化成为Apache顶级项目（TLP），成为国内首个互联网中间件在Apache上的顶级项目。

官网地址：[Apache RocketMQ](http://rocketmq.apache.org/)

### 2、RocketMQ发展历程

![RocketMQ发展历程](150632_USCe_2720166.png)

2007年，阿里开始五彩石项目，Notify作为项目中交易核心消息流转系统，应运而生。Notify系统是RocketMQ的雏形。

2010年，B2B大规模使用ActiveMQ作为阿里的消息内核。阿里急需一个具有海量堆积能力的消息系统。

2011年初，Kafka开源，淘宝中间件团队在对Kafka进行了深入研究后，开发了一款新的MQ，MetaQ。

2012年，MetaQ发展到了v3.0版本，在它基础上进行了进一步的抽象，形成了RocketMQ，然后就将其进行了开源。

2015年，阿里在RocketMQ的基础上，又推出了一款针对阿里云上用户的消息系统AliwareMQ。

2016年11月28日，阿里巴巴向Apache软件基金会捐赠RocketMQ，成为Apache孵化项目。

2017年9月25日，Apache宣布RocketMQ孵化成为Apache顶级项目（TLP），成为国内首个互联网中间件在Apache上的顶级项目。

# 第2章 RocketMQ的安装与启动

## 一、基本概念

### 1 消息（Message）

消息是指，消息系统所传输的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。

### 2 主题（Topic）

Topic表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。

topic:message 1:n

message:topic 1:1

一个生产者可以同时发送多种Topic消息；而一个消费者只能对某种特定的Topic感兴趣，即只可以订阅和消费一种Topic的消息。

producer:topic 1:n

consumer:topic 1:1

### 3 标签（Tag）

为消息设置的标签，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题下设置不同标签。标签能够有效地保持代码的清晰度和连贯性，并优化RocketMQ提供的查询系统。消费者可以根据tag实现对不同子主题的不同消费逻辑，实现更好的扩展性。

### 4 队列（Queue）

存储消息的物理实体。一个Topic中可以包含多个Queue，每个Queue中存放的就是该Topic的消息。一个Topic的Queue也被称为一个Topic中消息的分区（Partition）。

<font color="#FF0000">一个Topic的Queue中的消息只能被一个消费组中的一个消费者消费。</font>

其他参考资料中还会看到一个概念：分片（Sharding)。分片不同于分区。在RocketMQ中，分片指的是存放相应Topic的Broker的数量。每个分片中会创建出相应数量的分区，即Queue，每个Queue的大小都是相同的。（不是官方概念）

![Topic,Topic分片和Queue](20170616154817226.png)

### 5 消息标识（MessageId/Key）

RocketMQ中每个消息拥有唯一的MessageId，且可以携带具有业务标识的Key，以方便对消息的查询。不过需要注意的是，MessageId有两个：在生产者send()消息时会自动生成一个MessageId（msgId），当消息到达Broker后，Broker也会自动生成一个MessageId(offsetMsgId)。msgId、offsetMsgId与key都称为消息标识。

+ msgId：由producer端生成，其生成规则为：

  producerIp + 进程pid + MessageClientIDSetter类的classLoader的hashCode + 当前时间 + AutomicInteger自增计数器

+ offsetMsgId：由broker端生成，其生成规则为：brokerIp + 物理分区的offset

+ key：由用户指定的业务相关的唯一标识。

## 二、系统架构

![RocketMQ系统架构](RocketMQ系统架构.png)

RocketMQ架构上主要由四部分构成：

### 1 Producer

消息生产者，负责生产消息。Producer通过MQ的负载均衡模块选择相应的Broker集群队列进行消息投递，投递的过程支持快速失败并且低延迟。

RocketMQ中的消息生产者都是以生产者组（Producer Group）的形式出现的。生产者组是同一类生产者的集合，这类Producer发送相同Topic类型的消息。一个生产者组可以同时发送多个主题的消息。

### 2 Consumer

消息消费者，负责消费消息。一个消息消费者会从Broker服务器中获取到消息，并对消息进行相关业务处理。

RocketMQ中的消息消费者都是以消费者组（Consumer Group）的形式出现的。消费者组是同一类消费者的集合，这类Consumer消费的是同一个Topic类型的消息。消费者组使得在消息消费方面，实现**负载均衡**（将一个Topic中的不同的Queue平均分配给同一个Consumer Group中的不同Consumer，并不是将消息负载均衡）和**容错**（一个Consumer挂了，该Consumer Group中的其它Consumer可以接着消费原Consumer消费的Queue）的目标变得非常容易。

消费者组中Consumer的数量应该小于等于订阅Topic的Queue数量。如果超出Queue数量，则多出的Consumer将不能消费消息。

一个Topic类型的消息可以被多个消费者组同时消费。

### 3 Name Server

#### 功能介绍

NameServer是一个Broker与Topic路由的注册中心，支持Broker的动态注册与发现。

RocketMQ的思想来自于Kafka，而Kafka是依赖了Zookeeper的，所以，在RocketMQ的早期版本，即在MetaQ v1.0与v2.0版本中，也是依赖于Zookeeper的。从MetaQ v3.0，即RocketMQ开始去掉了Zookeeper依赖，使用了自己的NameServer。



主要包括两个功能：

+ Broker管理：接受Broker集群的注册信息并且保存下来作为路由信息的基本数据；提供心跳检测机制，检查Broker是否还存活。
+ 路由信息管理：每个NameServer中都保存着Broker集群的整个路由信息和用于客户端查询的队列信息。Producer和Consumer通过NameServer可以获取整个Broker集群的路由信息，从而进行消息的投递和消费。

#### 路由注册

NameServer通常也是以集群的方式部署，不过，NameServer是无状态的，即NameServer集群中的各个节点间是无差异的，各节点间互相不进行信息通讯。那各节点中的数据是如何进行数据同步的呢？在Broker节点启动时，轮询NameServer列表，与每个NameServer节点建立长连接，发起注册请求。在NameServer内部维护着一个Broker列表，用来动态存储Broker的信息。

> 注意，这是与其它像zk、Eureka、Nacos等注册中心不同的地方。
>
> 这种NameServer的无状态方式的优缺点：
>
> 优点：NameServer集群搭建简单。
>
> 缺点：对于Broker，必须明确指出所有NameServer地址，否则未指出的将不会去注册，也正因为如此，NameServer并不能随便扩容。若Broker不重新配置，新增的NameServer对于Broker来说是不可见的，其不会向这个NameServer进行注册。

Broker节点为了证明自己是活着的，为了维护与NameServer间的长连接，会将最新的信息以心跳包的方式上报给NameServer，每30秒发送一次心跳。心跳包中包含BrokerId、Broker地址(IP+Port)、Broker名称、Broker所属集群名称等等。NameServer在接收到心跳包后，会更新心跳的时间戳，记录这个Broker的最新存活时间。

#### 路由剔除

由于Broker关机、宕机或网络抖动等原因，NameServer没有收到Broker的心跳，NameServer可能会将其从Broker列表中剔除。

NameServer中有一个定时任务，每隔10秒就会扫描一次Broker表，查看每一个Broker的最新心跳时间戳距离当前时间是否超过120秒，如果超过，则会判定Broker失效，然后将其从Broker列表中剔除。

#### 路由发现

RocketMQ的路由发现采用的是Pull模型。当Topic路由信息出现变化时，NameServer不会主动推送给客户端，而是客户端定时拉取最新的路由。默认客户端每30秒会拉取一次最新的路由。

> 扩展：
>
> （1）Push模型：推送模型，其实时性较好，是一个“发布-订阅”模型，需要维护一个长连接，而长连接的维护是需要资源成本的。该模型适合的场景：
>
> + 实时性较高
> + Client数量不多，Server数据变化较频繁
>
> （2）Pull模型：拉取模型。存在的问题：实时性较差。
>
> （3）Long Polling模型：长轮询模型。利用以上两种模型的优势。

#### 客户端NameServer选择策略

> 这里的客户端指的是Producer和Consumer

客户端在配置时必须要写上NameServer集群的地址，那么客户端到底连接的是哪个NameServer节点呢？客户端首先会产生一个随机数，然后再与NameServer节点数量取模，此时得到的就是所要连接的节点索引，然后就会进行连接。如果连接失败，则会采用round-robin策略，逐个尝试着去连接其它节点。

### 4 Broker

#### 功能介绍

Broker充当着消息中转角色，负责存储消息、转发消息。Broker在RocketMQ系统中负责接收并存储从生产者发送来的消息，同时为消费者的拉取请求作准备。Broker同时也存储着消息相关的元数据，包括消费者组消费进度偏移offset、主题、队列等。



#### 模块构成

![模块构成](image-20211206191332201.png)

Remoting Module：整个Broker的实体，负责处理来自clients端的请求。而这个Broker实体则由以下模块构成。

Client Manager：客户端管理器。负责接收、解析客户端（Producer/Consumer）请求，管理客户端。例如，维护Consumer的Topic订阅信息。

Store Service：存储服务。提供方便简单的API接口，处理**消息存储到物理硬盘**和**消息查询**功能。

HA Service：高可用服务，提供Master Broker和Slave Broker之间的数据同步功能。

Index Service：索引服务。根据特定的Message Key，对投递到Broker的消息进行索引服务，同时也提供根据Message Key对消息进行快速查询的功能。

#### 集群部署

为了增强Broker性能与吞吐量，Broker一般都是以集群形式出现的。各集群节点中可能存放着相同Topic的不同Queue。不过，这里有一个问题，如果某Broker节点宕机，如何保证数据不丢失呢？其解决方案是，将每个Broker集群节点进行横向扩展，即将Broker节点再建为一个HA集群，解决单点问题。

Broker节点集群是一个主从集群，即集群中具有Master与Slave两种角色。Master负责处理读写操作请求，Slave负责对Master中的数据进行备份，当Master挂掉，Slave会自动切换为Master去工作。一个Master可以包含多个Slave，但一个Slave只能隶属一个Master。Master与Slave的对应关系是通过指定相同的BrokerName、不同的BrokerId来确定的。BrokerId为0表示Master，非0表示Slave。每个Broker与NameServer集群中的所有节点建立长连接，定时注册Topic信息到所有NameServer。

### 5 工作流程

#### 具体流程

1）启动NameServer，NameServer启动后开始监听端口，等待Broker、Producer、Consumer连接。

2）启动Broker时，Broker会与所有的NameServer建立并保持长连接，然后每30秒向NameServer定时发送心跳。

3）发送消息前，可以先创建Topic，创建Topic时需要指定该Topic要存储在哪些Broker上，当然，在创建Topic时也会将Topic与Broker的关系写入到NameServer中。不过，这步是可选的，也可以在发送消息时自动创建Topic。

4）Producer发送消息，启动时先跟NameServer集群中的其中一台建立长连接，并从NameServer中获取路由信息，即当前发送的Topic的Queue与Broker的地址（IP+Port）的映射关系。然后根据算法策略从队选择一个Queue，与队列所在的Broker建立长连接从而向Broker发消息。当然，在获取到路由信息后，Producer会首先将路由信息缓存到本地，再每30秒从NameServer更新一次路由信息。

5）Consumer跟Producer类似，跟其中一台NameServer建立长连接，获取其所订阅Topic的路由信息，然后根据算法策略从路由信息中获取到其所要消费的Queue，然后直接跟Broker建立长连接，开始消费其中的消息。Consumer在获取到路由信息后，同样也会每30秒从NameServer更新一次路由信息。不过不同于Producer的是，Consumer还会向Broker发送心跳，以确保Broker的存活状态。

#### Topic的创建模式

手动创建Topic是，有两种模式：

- 集群模式：该模式下创建的Topic在该集群中，所有Broker中的Queue数量是相同的。
- Broker模式：该模式下创建的Topic在该集群中，每个Broker中的Queue数量可以不同。

自动创建Topic时，默认采用的是Broker模式，会为每个Broker默认创建4个Queue。

#### 读/写队列

从物理上来讲，读/写队列是同一个队列。所以，不存在读/写队列数据同步问题。读/写队列是逻辑上进行区分的概念。一般情况下，读/写队列是相同的。

例如，创建Topic时设置的写队列数量为8，读队列数量为4，此时系统会创建8个Queue，分别是0-7。Producer会将消息写入到这8个队列，但Consumer只会消费0-3这4个队列中的消息，4-7中的消息不会被消费到。

写队列为4，读队列为8，也会创建8个Queue。Producer会将消息写入到0-3这4个队列，Consumer会消费0-7这8个队列中的消息，但是4-7中是没有消息的。

也就是说，当读/写队列数量设置不同时，总是有问题的，那么为什么这样设计？

目的是为了方便Topic的Queue的缩容。

pem用于设置对当前创建Topic的操作权限：2表示只写，4表示只读，6表示读写。

## 三、单机安装与启动

[Quick Start - Apache RocketMQ](https://rocketmq.apache.org/docs/quick-start/)



## 四、控制台的安装与启动（dashboard）

### 1.下载

### 2.修改配置

### 3.添加依赖

### 4.打包

### 5.启动

### 6.访问

## 五、集群搭建理论

p27

### 1.数据复制与刷盘策略

#### 复制策略

复制策略是Broker的Master与Slave间的数据同步方式。分为同步复制与异步复制：

- 同步复制：消息写入master后，master会等待slave同步数据成功后才向producer返回成功ACK
- 异步复制：消息写入master后，master会立即向producer返回成功ACK，无需等待slave同步数据成功

>异步复制策略会降低系统的写入延迟，RT变小，提高了系统的吞吐量

#### 刷盘策略

刷盘策略指的是broker中消息落盘方式，即消息发送到broker内存后消息持久化到磁盘的方式。分为同步刷盘与异步刷盘：

- 同步刷盘：当消息持久化到broker的磁盘后才算是消息写入成功。
- 异步刷盘：当消息写入到broker的内存后即表示消息写入成功，无需等待消息持久化到磁盘。

>异步刷盘策略会降低系统的写入延迟，RT变小，提高了系统的吞吐量

### 2.Broker集群模式

根据Broker集群中各个节点间关系的不同，Broker集群可以分为以下几类：

#### 单Master

只有一个broker。这种方式也只能是在测试时使用，生产环境下不能使用，因为存在单点问题。

#### 多Master

broker集群仅由多个master构成，不存在Slave。同一台Topic的各个Queue会分布在各个master节点上。

- 优点：配置简单，单个Master宕机或重启维护对应用无影响，在磁盘配置为RAID10时，即使机器宕机不可恢复情况下，由于RAID10磁盘非常可靠，消息也不会丢（异步刷盘丢失少量消息，同步刷盘一条不丢），性能最高。
- 缺点：单台机器宕机期间，这台机器上未被消费的消息在机器恢复之前不可订阅（不可消费），消息实时性会受到影响。

> 以上优点的前提是，这些master都配置了RAID磁盘阵列。如果没有配置，一旦出现某Master宕机，则会发生大量消息丢失的情况。

#### 多Master多Slave模式-异步复制

broker集群由多个master构成，每个master又配置了多个slave（在配置了RAID磁盘阵列的情况下，一个master一般配置一个slave即可）。master与slave的关系是主备关系，即master负责处理消息的读写请求，而slave仅负责消息的备份与master宕机后的角色切换。

异步复制即前面**复制策略**中的**异步复制策略**，即消息写入master成功后，master立即向producer返回成功ACK，无需等待slave同步数据成功。

该模式的最大特点之一是，当master宕机后slave能够自动切换为master。不过由于slave从master的同步具有短暂的延迟（毫秒级），所以当master宕机后，这种异步复制方式可能会存在少量消息的丢失问题。

#### 多Master多Slave模式-同步双写

该模式是多Master多Slave模式的同步复制实现。所谓同步双写，指的是消息写入master成功后，master会等待slave同步数据成功后才向producer返回成功ACK，即master与slave都要写入成功后才会返回成功ACK，也即双写。

该模式与异步复制模式相比，优点是消息的安全性更高，不存在消息丢失的情况。但单个消息的RT略高，从而导致性能要略低（大约低10%）。

该模式存在一个大问题：对于目前的版本，Master宕机后，Slave不能自动切换到Master。

#### 最佳实践

一般会为Master配置RAID10磁盘阵列，然后再为其配置一个Slave。即利用了RAID10磁盘阵列的高效、安全性，又解决了可能会影响订阅的问题。

>1)RAID磁盘阵列的效率要高于Master-Slave集群。因为RAID是硬件支持的。也正因如此，RAID阵列的搭建成本较高。
>
>2）多Master+RAID阵列，与多Master多Slave集群的区别是什么？
>
>- 多Master+RAID阵列，其仅仅可以保证数据不丢失，即不影响消息写入，但其可能会影响到消息的订阅。但其执行效率要远高于多Master多Slave集群。
>- 多Master多Slave集群，其不仅可以保证数据不丢失，也不会影响消息写入。其运行效率要低于多Master+RAID阵列。

p31 51s

## 六、磁盘阵列RAID（补充）

## 七、集群搭建实践

## 八、mqadmin命令

# 第3章 RocketMQ工作原理

![RocketMQ工作原理](image-20211229175925307.png)

## 一、消息的生产

### 1.消息的生产过程

Producer可以将消息写入到某Broker中的某Queue中，其经历了如下过程：

- Producer发送消息前，会先向NameServer发出获取消息Topic的路由信息的请求
- NameServer返回该Topic的路由表及Broker列表
- Producer根据代码中指定的Queue选择策略，从Queue列表中选出一个队列，用于后续存储消息
- Producer对消息做一些特殊处理，例如，消息本身超过4M，则会对其进行压缩
- Producer向选择出的Queue所在的Broker发出RPC请求，将消息发送到选择出的Queue

> 路由表：实际是一个Map，key为topic名称，value是一个QueueData实例列表。QueueData并不是一个Queue对应一个QueueData，而是一个Broker中该Topic的所有Queue对应一个QueueData。即，只要涉及到该Topic的Broker，一个Broker对应一个QueueData。QueueData中包含brokerName。简单来说，路由表的key为Topic名称，value则为所有涉及该Topic的BrokerName列表。
>
> 
>
> Broker列表：其实际也是一个Map。key为brokerName，value为BrokerData。一套brokerName名称相同的Master-Slave小集群对应一个BrokerData。BrokerData中包含brokerName及一个map。该map的key为brokerId，value为该broker对应的地址。brokerId为0表示该broker为Master，非0表示Slave。



### 2.Queue选择算法

对于无序消息，其Queue选择算法也称为消息投递算法，常见的有两种：

#### 轮询算法

默认选择算法。该算法保证了每个Queue中可以均匀地获取到消息。

> 该算法存在一个问题：由于某些原因，在某些Broker上的Queue可能投递延迟较严重。从而导致Producer的缓存队列中出现较大的消息积压，影响消息的投递性能。

#### 最小投递延迟算法

该算法会统计每次消息投递的时间延迟，然后根据统计出的结果将消息投递到时间延迟最小的Queue。如果延迟相同，则采用轮询算法投递

> 该算法也存在一个问题：消息在Queue上的分配不均匀。投递延迟小的Queue其可能会存在大量的消息。而对该Queue的消费者压力会增大，降低消息的消费能力，可能会导致MQ中消息的堆积。

## 二、消息的存储

RocketMQ中的消息存储在本地文件系统中，这些相关文件默认在当前用户主目录`%USERPROFILE%，即C:\Users\CZH`下的store目录中。

- abort：该文件在Broker启动后会自动创建，正常关闭Broker，该文件会自动消失，如果broker异常退出，则文件会一直存在，在启动时会走其他流程进行文件修复等。

- checkpoint：其中存储着commitlog、consumequeue、index文件的最后刷盘时间戳。

- commitlog：存放消息实体，所有topic的消息都会通过追加的方式往commitlog文件中写入，单文件大小默认为1G，文件名为起始偏移量，长度为20位，左边补零。

- config：存放topic和订阅组的配置信息，以及消费进度等。

- consumequeue：存放消息消费队列，只有写入到这里的数据，才能够被消费者消费，每个topic都会在此目录下创建一个同名目录，每个队列会建立对应的索引文件，用于加快消息的检索和节省磁盘空间，里面存放了消息的关键信息，如commitog文件中的偏移量、消息长度、tag的hashcode值等。

- index：存放消息索引文件，只有写入到这里的数据，才能够通过key或者msgId等进行查询。

- lock：运行期间使用到的全局资源锁，在consumer进行rebalance的时候。



1.commitlog文件

p43



## 三、indexFile

## 四、消息的消费

消费者从Broker中获取消息的方式有两种：pull拉取方式和push推动方式。消费者组对于消息消费的模式又分为两种：集群消费Clustering和广播消费Broadcasting。

### 1.获取消费类型

#### 拉取式消费

Consumer主动从Broker中拉取消息，主动权由Consumer控制。一旦获取了批量消息，就会启动消费过程。不过，该消息的实时性较弱，即Broekr中有了新的消息时消费者并不能及时发现并消费。

> 拉取时间间隔由用户指定，所以在设置该间隔时需要注意平衡：间隔太短，空请求比例会增加；间隔太长，消息的实时性太差。

#### 推送式消费

该模式下Broker收到数据后会主动推送给Consumer。该消费模式一般实时性较高。

该消费类型是典型的`发布-订阅`模式，即Consumer向其关联的Queue注册了监听器，一旦发现有新的消息到来就会触发回调的执行，回调方法是Consumer去Queue中拉取消息，而这些都是基于Consumer与Broker间的长连接的，长连接的维护是需要消耗系统资源的。

对比

- pull：需要应用去实现对关联Queue的遍历，实时性差；但便于应用控制消息的拉取。
- push：封装了对关联Queue的遍历，实时性强，但会占用较多的系统资源。

### 2.消费模式

#### 广播消费

广播消费模式下，相同Consumer Group的每个Consumer实例都接收同一个Topic的全量的消息。即每条消息都会被发送到Consumer Group中的`每个`Consumer。

#### 集群消费

集群消费模式下,相同Consumer Group的每个Consumer实例`平均分摊`同一个Topic的消息。即每条消息只会被发送到Consumer Group中的`某个`Consumer。

#### 消息进度保存

- 广播模式：消息进度保存在consumer端。因为广播模式下consumer group中每个consumer都会消费所有消息，但它们的消费进度是不同的，所以consumer各自保存各自的消费进度。
- 集群模式：消费进度保存在Broker中。consumer group中的所有consumer共同消费同一个Topic中的消息，同一条消息只会被消费一次。消费进度会参与到消费的负载均衡中，故消费进度是需要共享的。

### 3.Rebalance机制

Rebalance机制讨论的前提是`集群消费模式`

#### 什么是Rebalance

Rebalance即再均衡，指的是将一个Topic下的多个Queue在同一个Consumer Group中的多个Consumer间进行重新分配的过程。

![RocketMQ工作原理](image-20211229175925307.png)

Rebalance机制的本意是为了提升消息的并行消费能力。例如，一个Topic下5个队列，在只有一个消费者的情况下，这个消费者将负责消费这5个队列的消息。如果此时我们增加一个消费者，那么就可以给其中一个消费者分配2个队列，给另一个分配3个队列，从而提升消息的并行消费能力。

#### Rebalance限制

由于一个队列最多分配给一个消费者，因此当某个消费者组下的消费者实例数量大于队列的数量时，多余的消费者实例将分配不到任何队列。

#### Rebalance危害

**消费暂停：**在只有一个Consumer时，其负责消费所有队列，在新增了一个Consumer后会触发Rebalance的发生。此时原Consumer就需要暂停部分队列的消费，等到这些队列分配给新的Consumer后，这些暂停消费的队列才能继续被消费。

**消费重复：**Consumer在消费分配给自己的队列时，必须接着之前Consumer提交的消费进度的offset继续消费。然而默认情况下，offset是异步提交的，这个异步性导致提交到Broker的offset与Consumer实际消费的消息并不一致。这个不一致的差值就是可能会重复提交的消息。

> 同步提交：consumer提交了其消费完毕的一批消息的offset给broker后，需要等待broker的成功ACK。当收到ACK后，consumer才会继续获取并消费下一批消息（从ack中获取nextBeginOffset）。获取ACK超时会重新提交offset，直到获取到响应。在等待ACK期间，consumer是阻塞的（严重影响吞吐量）。
>
> 
>
> 异步提交：consumer提交了其消费完毕的一批消息的offset给broker后，不需要等待broker的成功ACK。consumer可以直接获取并消费下一批消息。

**消费突刺：**由于Rebalance可能导致重复消费，如果需要重复消费的消息过多，或者因为Rebalance暂停时间过长从而导致积压了部分消息。那么有可能会导致在Rebalance结束之后瞬间需要消费很多消息。

#### Rebalance产生的原因

导致Rebalance产生的原因无非就两个：消费者所订阅Topic的Queue数量发生变化，或消费者组中消费者数量发生变化。

#### Rebalance过程

在Broker中维护着多个Map集合，这些集合中动态存放着当前Topic中Queue的信息、Consumer Group中Consumer实例的信息。一旦发现消费者所订阅的Queue数量发生变化，或消费者组中消费者的数量发生变化，立即向Consuemr Group中的每个实例发出Rebalance通知。

Consumer实例在接收到通知后会采用`Queue分配算法`自己获取到相应的Queue，即由Consumer实例自主进行Rebalance。

#### 与Kafka对比

在Kafka中，一旦发现出现了Rebalance条件，Broker会调用Group Coordinator来完成Rebalance。Coordinator是Broker中的一个进程。Coordinator会在Consumer Group中选出一个Group Leader。由这个Leader根据自己本身组情况完成Partition分区的再分配。这个再分配结果会上报给Coordinator，并由Coordinator同步给Group中的所有Consumer实例。

Kafka中的Rebalance是由Consumer Leader完成的。而RocketMQ中Rebalance是由每个Consumer自身完成的，Group中不存在Leader。

### 4.Queue分配算法

一个Topic中的Queue只能由Consumer Group中的一个Consumer进行消费，那么它们间的配对关系是如何确定的，即Queue要分配给哪个Consumer进行消费？也是有算法策略的。常见的有4种策略。这些策略是通过在创建Consumer时的构造器传进去的。

#### 平均分配策略

该算法是根据`avg = QueueCount / ConsumerCount`的计算结果进行分配的。如果能够整除，则按顺序将avg个Queue逐个分配给Consumer；如果不能整除，则将多余出的Queue按照Consumer顺序逐个分配。

> 该算法即，先计算好每个Consumer应该分得几个Queue，然后再依次将这些数量的Queue逐个分配。

#### 环形平均策略

![环形平均策略](image-20211229175054146.png)

环形平均算法是指，根据消费者的顺序，依次在由queue队列组成的环形图中逐个分配。

#### 一致性hash策略

![一致性hash策略](image-20211229175134329.png)

该算法会将consumer的hash值作为Node节点存放到hash环上，通过顺时针方向，距离queue最近的那个consumer就是该queue要分配的consumer。

应用场景：

Consumer数量变化较频繁的场景。

> 缺点：分配不均
>
> 优点：队列扩缩容或Consumer数量变化频繁时变化小

#### 同机房策略

![同机房策略](image-20211229175233233.png)

该算法会根据Queue的部署机房位置和Consumer的位置，过滤出当前相同机房的Queue，然后按照平均分配或环形平均策略对同机房的Queue进行分配，如果没有同机房的Queue，则会按照平均策略或者环形平均策略对所有的Queue进行分配。

### 5.至少一次原则

RocketMQ有一个原则，每条消息必须被成功消费一次。

什么是消费成功呢？Consumer在消息消费完成后会向其消费进度记录器提交消费消息的offset，offset被成功记录到记录器中，那么这条消息就被成功消费了。

## 五、订阅关系的一致性

订阅关系一致指的是同一个消费者Group ID下所有Consumer实例所订阅的Topic、Tag必须完全一致。如果订阅关系不一致，消息消费的逻辑就会混乱，甚至导致消息丢失。

[订阅关系一致 (aliyun.com)]([订阅关系一致 (aliyun.com)](https://help.aliyun.com/document_detail/43523.html))

## 六、offset管理

p61

## 七、消费幂等

### 1.什么是消费幂等

当出现消费者对某条消息重复消费的情况时，重复消费的结果与消费一次的结果是相同的，并且多次消费并未对业务系统产生任何负面影响，那么这个消费过程就是消费幂等的。

> 幂等：若某操作执行多次与执行一次对系统产生的影响是相同的，则称该操作是幂等的。

在互联网应用中，尤其在网络不稳定的情况下，消息很有可能会出现重复发送或重复消费。如果重复的消息可能会影响业务处理，那么就应该对消息做幂等处理。

### 2.消息重复的场景分析

什么情况下可能会出现消息被重复消费呢？最常见的有以下三种情况：

#### 发送时消息重复

当一条消息已被成功发送到Broker并完成持久化，此时出现了网络闪断，从而导致Broker对Producer应答失败。如果此时Producer意识到消息发送失败并尝试再次发送消息，此时Broker中就可能会出现两条内容相同并且Message ID也相同的消息，那么后续Consumer就一定会消费两次该消息。

#### 消费时消息重复

消息已投递到Consumer并完成业务处理，当Consumer给Broker反馈应答时网络闪断，Broker没有接收到消费成功响应。为了保证消息`至少被消费一次`的原则，Broker将在网络恢复后再次尝试投递之前已被处理过的消息。此时消费者就会收到与之前处理过的内容相同、Message ID也相同的消息。

#### Rebalance时消息重复

当Consumer Group中的Consumer数量发生变化时，或其订阅的Topic的Queue数量发生变化时，会触发Reblance，此时Consumer可能会收到曾经被消费过的消息。

### 3.通用解决方案

#### 两要素

幂等解决方案的设计中涉及到两项要素：幂等令牌，与唯一性处理。只要充分利用好这两要素，都可以设计出好的幂等解决方案。

- 幂等令牌：是生产者和消费者两者中的既定协议，通常指具备唯一业务标识的字符串。例如，订单号、流水号。
- 唯一性处理:服务端通过采用一定的算法策略，保证同一个业务逻辑不会被重复执行成功多次。

#### 解决方案

对于常见的系统，幂等性操作的通用性解决方案是：

1.首先通过缓存去重。在缓存中如果已经存在了某幂等令牌，则说明本次操作是重复性操作；若缓存没有命中，则进入下一步。

2.在唯一性处理之前，先在数据库中查询幂等令牌作为索引的数据是否存在。若存在，则说明本次操作为重复性操作；若不存在，则进入下一步。

3.在同一事务中完成三项操作：唯一性处理后，将幂等令牌写入到缓存，并将幂等令牌作为唯一索引的数据写入到DB中。

> 第1步已经判断过是否是重复性操作了，为什么第2步还要再次判断？能够进入第2步，说明已经不是重复操作了，第二次判断是否重复？
>
> 不重复，一般缓存中的数据是具有有效期的。缓存中数据的有效期一旦过期，就是发生缓存穿透，使请求直接到达了DBMS。

## 八、消息堆积与消息延迟

### 1.概念

消息处理流程中，如果Consumer的消费速度跟不上Producer的发送速度，MQ中未处理的消息会越来越多，这部分消息就被称为堆积消息。消息出现堆积进而会造成消息的消费延迟。以下场景需要重点关注消息堆积和消费延迟问题：

- 业务系统上下游能力不匹配造成的持续堆积，且无法自行恢复。
- 业务系统对消息的消费实时性要求较高，即使是短暂的堆积造成的消息延迟也无法接受。

### 2.产生原因分析

Consumer使用长轮询Pull模式消费消息时，分为以下两个阶段：

#### 消息拉取

Consumer通过长轮询Pull模式批量拉取的方式从服务端获取消息，将拉取到的消息缓存到本地缓冲队列中。对于拉取式消费，在内网环境下会有很高的吞吐量，所以这一阶段一般不会成为消息堆积的瓶颈。

#### 消息消费

Consumer将本地缓存的消息提交到消费线程中，使用业务消费逻辑对消息进行处理，处理完毕后获取到一个结果。这是真正的消息消费过程。此时Consumer的消费能力就完全依赖于消息的`消费耗时`和`消费并发度`了。如果由于业务处理逻辑复杂等原因，导致处理单条消息的耗时较长，则整体的消息吞吐量肯定不会高，此时就会导致Consumer本地缓冲队列达到上限，停止从服务端拉取消息。

#### 结论

消息堆积的主要瓶颈在于客户端的消费能力，而消费能力由`消费耗时`和`消费并发度`决定。

### 3.消费耗时

影响消息处理时长的代码逻辑，可能主要产生于两种类型的代码：CPU内部计算型代码和外部I/O操作型代码。

通常情况下代码中如果没有复杂的递归和循环的话，内部计算耗时相对外部I/O操作来说几乎可以忽略。所以外部IO型代码是影响消息处理时长的主要症结所在。

### 4.消费并发度

一般情况下，消费者端的消费并发度由单节点线程数和节点数量共同决定，其值为`单节点线程数`*`节点数量`。不过，通常需要优先调整单节点的线程数，若单机硬件资源达到了上限，则需要通过横向扩展来提高消费并发度。

> 单节点线程数：单个Consumer所包含的线程数量
>
> 节点数量：Consumer Group所包含的Consumer数量
>
> 对于普通消息、延时消息及事务消息，并发度计算都是`单节点线程数`*`节点数量`。但对于顺序消息则是不同的。顺序消息的消息并发度等于Topic的Queue分区数量。

### 5.单机线程数计算

对于一台主机中线程池中线程数的设置需要谨慎，不能盲目直接调大线程数，设置过大的线程数反而会带来大量的线程切换的开销。理想环境下单节点的最优线程数计算模型为：C*(T1+T2)/T1。

- C：CPU内核数
- T1：CPU内部逻辑计算耗时
- T2：外部IO操作哦耗时

## 九、消息的清理

消息被消费后会被清理掉吗？不会

消息是被顺序存储在commitlog文件的，且消息大小不定长，所以消息的清理是不可能以消息为单位进行清理的，而是以commitlog文件为单位进行清理的。否则会急剧下降清理效率，并实现逻辑复杂。

commitlog文件存在一个`过期时间`，默认为72小时，即3天。除了用户手动清理外，在以下情况下也会被自动清理，无论文件中的消息是否被消费过：

- 文件过期，且到达`清理时间点`（默认为凌晨4点）后，自动清理过期文件。
- 文件过期，且磁盘空间占用率已达`过期清理警戒线`（默认75%）后，无论是否达到清理时间点，都会自动清理过期文件。
- 磁盘占用率达到`清理警戒线`（默认85%）后，开始按照设定好的规则清理文件，无论是否过期。默认会从最老的文件开始清理。
- 磁盘占用率达到`系统危险警戒线`（默认90%）后，Broker将拒绝消息写入。



# 第4章 RocketMQ应用 

## 一、普通消息

Producer对于消息的发送方式有多种选择，不同的方式会产生不同的系统效果。

### 1.消息发送分类

#### 同步发送消息

同步发送消息是指，Producer发出一条消息后，会在收到MQ返回的ACK之后才发下一条消息。该方式的可靠性最高，但消息发送效率太低。

#### 异步发送消息

异步发送消息是指，Producer发出消息后无需等待MQ返回ACK，直接发送下一条消息。该方式的消息可靠性可以得到保障，消息发送效率也可以。

#### 单向发送消息

单向发送消息是指，Producer仅负责发送消息，不等待、不处理MQ的ACK。使用该发送方式时，MQ也不返回ACK。该方式的消息发送效率最高，但消息可靠性较差。



### 2.代码举例

同步消息发送生产者

```java
//同步发送消息
public class SyncProducer {
    public static void main(String[] args) throws Exception {
        //创建一个producer，参数为Producer Group名称
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        //指定nameServer地址
        producer.setNamesrvAddr("localhost:9876");
        //设置当发送失败时重试发送的次数，默认为2次
        producer.setRetryTimesWhenSendFailed(3);
        //设置发送超时时限为5s，默认3秒
        producer.setSendMsgTimeout(5000);
        //开启生产者
        producer.start();

        //生产并发送10条消息
        for (int i = 0; i < 10; i++) {
            byte[] body = ("Hi," + i).getBytes();
            Message msg = new Message("someTopic", "someTag", body);
            //发送消息
            SendResult sendResult = producer.send(msg);
            System.out.println(sendResult);
        }
        //关闭producer
        producer.shutdown();
    }
}
```



异步消息发送生产者

```java
public class AsyncProducer {
    public static void main(String[] args) throws Exception {
        //创建一个producer，参数为Producer Group名称
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        //指定nameServer地址
        producer.setNamesrvAddr("localhost:9876");
        //指定异步发送失败后不进行重试
        producer.setRetryTimesWhenSendAsyncFailed(0);
        //指定新创建的Topic的Queue数量为2，默认为4
        producer.setDefaultTopicQueueNums(2);
        //开启生产者
        producer.start();

        for (int i = 0; i < 10; i++) {
            byte[] body = ("Hi," + i).getBytes();
            try {
                Message msg = new Message("myTopic", "mytag", body);
                //异步发送指定回调
                producer.send(msg, new SendCallback() {
                    @Override
                    public void onSuccess(SendResult sendResult) {
                        System.out.println(sendResult);
                    }

                    @Override
                    public void onException(Throwable throwable) {
                        throwable.printStackTrace();
                    }
                });
            } catch (Exception e){
                e.printStackTrace();
            }
        }

        //由于采用的是异步发送，如果不sleep，则消息还未发送就会将producer给关闭
        TimeUnit.SECONDS.sleep(3);
        producer.shutdown();
    }
}

```

单向消息发送生产者

```java
public class OnewayProducer {
    public static void main(String[] args) throws Exception {
        //创建一个producer，参数为Producer Group名称
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        //指定nameServer地址
        producer.setNamesrvAddr("localhost:9876");
        //开启生产者
        producer.start();

        for (int i = 0; i < 10; i++) {
            byte[] body = ("Hi," + i).getBytes();
            Message msg = new Message("single", "someTag", body);
            producer.sendOneway(msg);
        }
        producer.shutdown();
        System.out.println("producer shutdown");
    }
}
```

消息消费者

```java
public class SomeConsumer {
    public static void main(String[] args) throws MQClientException {
        //定义一个pull消费者
        //DefaultLitePullConsumer consumer = new DefaultLitePullConsumer("cg");
        //定义一个push消费者
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
        consumer.setNamesrvAddr("localhost:9876");
        //指定从第一条消息开始消费
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
        //指定消费topic与tag
        consumer.subscribe("someTopic", "*");
        //指定采用“广播模式”进行消费，默认为“集群模式”
        consumer.setMessageModel(MessageModel.BROADCASTING);

        //注册消息监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            //一旦broker中有了其订阅的消息就会触发该方法的执行
            //其返回值为当前consumer消费的状态
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext consumeConcurrentlyContext) {
                for (MessageExt messageExt : list) {
                    System.out.println(messageExt);
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        //开启消费者
        consumer.start();
        System.out.println("Consumer Started");
    }
}
```



## 二、顺序消息

### 1.什么是顺序消息

顺序消息指的是，严格按照消息的发送顺序进行消费的消息。

默认情况下生产者会把消息以Round Robin轮询方式发送到不同的Queue分区队列；而消费消息时会从多个Queue上拉取消息，这种情况下的发送和消费是不能保证顺序的。如果将消息仅发送到同一个Queue中，消费时也只能从这个Queue上拉取消息，就严格保证了消息的顺序性。

### 2.为什么需要顺序消息

例如，现在有TOPIC ORDER_STATUS，其下有4个Queue队列，该Topic中的不同消息用于描述当前订单的不同状态。假设订单有状态：未支付、已支付、发货中、发货成功、发货失败。

根据以上订单状态，生产者从时序上可以生成如下几个消息：

订单000001：未支付 --> 订单0001：已支付 --> 订单0001：发货中 --> 订单0001：发货失败



可以设计如下方案：对于相同订单号的消息，通过一定的策略，将其放置在一个Queue中，然后消费者再采用一定的策略（例如，一个线程独立处理一个queue，保证处理消息的顺序性），能够保证消费的顺序性。

### 3.有序性分类

根据有序范围的不同，RocketMQ可以严格地保证两种消息的有序性：`分区有序`与`全局有序`。

**全局有序**

当发送和消费参与的Queue只有一个时所保证的有序是整个Topic中消息的顺序，称为`全局有序`。

**分区有序**

![分区有序示意图](image-20211214112330003.png)

如果有多个Queue参与，其仅可保证在该Queue分区队列上的消息顺序，则称为`分区有序`

> 如何实现Queue的选择？在定义Producer是可以指定消息队列选择器，而这个选择器是我们自己实现了MessageQueueSelector接口定义的。
>
> 在定义选择器的选择算法时，一般需要使用选择key。这个选择key可以是消息key也可以是其他数据。但无论谁做选择key，都不能重复，都是唯一的。
>
> 一般性的选择算法是，让选择key与该topic所包含的queue的数量取模，其结果即为选择出的Queue的QueueId
>
> 取模算法存在一个问题：不同选择key与Queue数量取模结果可能是相同的，即不同选择key的消息可能会出现在相同的Queue，即同一个Consumer可能会消费到不同选择key的消息。这个问题如何解决？一般性的做法是，从消息中获取到选择key，对其进行判断。若是当前Consumer需要消费的消息，则直接消费，否则，什么也不做。这种做法要求选择key要能够随着消息一起被Consumer获取到。此时使用消息key作为选择key是比较好的做法。

### 4.代码举例

```java
public class OrderedProducer {
    public static void main(String[] args) throws Exception {
        //创建一个producer，参数为Producer Group名称
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        //指定nameServer地址
        producer.setNamesrvAddr("localhost:9876");
        //若为全局有序，则需要设置queue数量为1
        producer.setDefaultTopicQueueNums(1);
        //开启生产者
        producer.start();

        for (int i = 0; i < 10; i++) {
            Integer orderId = i;
            byte[] body = ("Hi," + i).getBytes();
            Message message = new Message("TopicA", "TagA", body);
            //将orderId作为消息key
            message.setKeys(orderId.toString());
            // send()的第三个参数值会传递给选择器的select()的第三个参数
            SendResult sendResult = producer.send(message, new MessageQueueSelector() {
                //具体的选择算法在该方法中定义
                @Override
                public MessageQueue select(List<MessageQueue> mqs, Message msg, Object arg) {
                    //一下是使用消息key作为选择的选择算法
                    String keys = msg.getKeys();
                    Integer id = Integer.valueOf(keys);

                    //一下是使用arg作为选择key的选择算法
                    //Integer id = (Integer) arg;

                    int index = id % mqs.size();
                    return mqs.get(index);
                }
            }, orderId);
            System.out.println(sendResult);
        }
        producer.shutdown();
    }
}
```

## 三、延时消息

### 1.什么是延时消息

当消息写入到Broker后，在指定的时长后才可被消费处理的消息，称为延时消息。

采用RocketMQ的延时消息可以实现定时任务的功能，而无需使用定时器。典型的应用场景是，电商交易中超时未支付关闭订单的场景，12306平台订票超时未支付取消订票的场景。

### 2.延时等级

延时消息的延迟时长`不支持随意时长的延迟`，是通过特定的延迟等级来指定的。延迟等级定义在RocketMQ服务端的`MessageStoreConfig`类中的如下变量中：

```java
private String messageDelayLevel = "1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h"
```

即，若指定的延时等级为3，则表示延迟时长为10s，即延时等级是从1开始计数的。

### 3.延时消息实现原理

#### 修改消息

Producer将消息发送到Broker后，Broker会首先将消息写入到commitlog文件，然后需要将其分发到相应的consumequeue。不过，在分发之前，系统会先判断消息中是否带有延时等级。若没有，则直接正常分发；若有则需要经历一个复杂的过程：

- 修改消息的Topic为`SCHEDULE_TOPIC_XXXX`

- 根据延时等级，在consumequeue目录中`SCHEDULE_TOPIC_XXXX`主题下创建出相应的queueId目录与consumequeue文件（如果没有这些目录与文件的话）

  >延迟等级delayLevel与queueId的对应关系为queueId = delayLevel - 1
  >
  >需要注意，在创建queueId目录时，并不是一次性地将所有延迟等级对应的目录全部创建完毕，而是用到哪个延迟等级创建哪个目录

- 修改消息索引单元的内容。索引单元中的Message Tag HashCode部分原本存放的是消息的Tag的Hash值。现修改为消息的投递时间。投递时间是指该消息被重新修改为原Topic后再次被写入到commitlog中的时间。投递时间 = 消息存储时间 + 延时等级时间。消息存储时间指的是消息被发送到Broker时的时间戳。

- 将消息索引写入到SCHEDULE_TOPIC_XXXX主题下相应的consumerqueue中。

#### 投递延时消息

Broker内部有一个延迟消息服务类，其会消费`SCHEDULE_TOPIC_XXXX`中的消息，即按照每条消息的投递时间，将延时消息投递到目标Topic中。不过，在投递之前会从commitlog中将原来写入的消息再次读出，并将其原来的延时等级设置为0，即原消息变为了一条不延迟的普通消息。然后再次将消息投递到目标Topic中。

> ScheduleMessageService在Broker启动时，会创建并启动一个定时器Timer，用于执行相应的定时任务。系统会根据延时等级的个数，定义相应数量的TimerTask，每个TimeTask负责一个延时等级消息的消费与投递。每个TimerTask都会检测相应Queue队列的第一条消息是否到期，若第一条消息未到期，则后面的所有消息更不会到期（消息是按照投递时间排序的）

#### 将消息重新写入commitlog

延迟消息服务类将延迟消息再次发送给了commitlog，并再次形成新的消息索引条目，分发到相应Queue。

### 4.代码举例

定义DelayProducer类

```java
public class DelayProducer {
    public static void main(String[] args) throws Exception {
        //创建一个producer，参数为Producer Group名称
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        //指定nameServer地址
        producer.setNamesrvAddr("localhost:9876");
        //开启生产者
        producer.start();

        for (int i = 0; i < 10; i++) {
            byte[] body = ("Hi," + i).getBytes();
            Message msg = new Message("TopicB", "TagB", body);
            SendResult sendResult = producer.send(msg);
            //指定消息延时等级
            msg.setDelayTimeLevel(3);
            System.out.print(new SimpleDateFormat("mm:ss").format(new Date()));
            System.out.println("," + sendResult);
        }
        producer.shutdown();
    }
}
```

## 四、事务消息

### 1.问题引入

需求场景：工行用户A向建行用户B转账1万元

使用同步消息处理需求场景

1. 工行系统发送一个给B增款1万元的同步消息M给Broker
2. 消息被Broker成功接收后，向工行系统发送成功ACK
3. 工行系统收到成功ACK后从用户A中扣款1万元
4. 建行系统从Broker中获取到消息M
5. 建行系统从Broker中获取到消息M，即向用户B中增加1万元

### 2.解决思路

p86

## 五、批量发送消息

### 1.批量发送消息

**发送限制**

生产者进行消息发送时可以一次发送多条消息，这可以大大提升Producer的发送效率。不过需要注意以下几点：

- 批量发送的消息必须具有相同的Topic
- 批量发送的消息必须具有相同的刷盘策略
- 批量发送的消息不能是延时消息与事务消息

**批量发送大小**

默认情况下，一批发送的消息不能超过4MB字节。如果想超出该值，有两种解决方案：

- 方案一：将批量消息进行拆分，拆分为若干不大于4M的消息集合分多次批量发送。
- 方案二：在Producer端与Broker端修改属性
  - Producer端需要在发送之前设置Producer的maxMessageSize属性
  - Broker端需要修改其加载的配置文件中的maxMessageSize属性

**生产者发送的消息大小**

生产者通过send()方法发送的message，并不是直接将Message序列化后发送到网络上的，而是通过这个Message生成了一个字符串发送出去的。这个字符串由四部分构成：Topic、消息Body、消息日志（占20字节），及用于描述消息的一堆属性key-value。这些属性中包含例如生产者地址、生产时间、要发送的QueueId等。最终写入到Broker中消息单元中的数据都是来自于这些属性。

### 2.批量消费消息

Consumer的MessageListenerConcurrently监听接口的consumeMessage()方法的第一个参数为消息列表，但默认情况下每次只能消费一条消息。若要使其一次可以消费多条消息，则可以通过修改Consumer的consumeMessageBatchMaxSize属性来指定。不过，该值不能超过32。因为默认情况下消费者每次可以拉取的消息最多是32条。若要修改一次拉取的最大值，则可通过修改Consuemr的pullBatchSize属性来指定。

**存在的问题**

Consumer的pullBatchSize属性与consumeMessageBatchMaxSize属性是否设置得越大越好？当然不是。

- pullBatchSize值设置的越大，Consumer每拉取一次需要的时间就会越长，且在网络上传输出现问题的可能性就越高。若在拉取过程中出现了问题，那么本批次所有的消息都需要全部重新拉取。
- consumeMessageBatchMaxSize值设置的越大，Consumer的消息并发消费能力越低，且这批被消费的消息具有相同的消费结果。因为consumeMessageBatchMaxSize指定的一批消息只会使用一个线程进行处理，且在处理过程中只要有一个消息处理异常，则这批消息需要全部重新再次消费处理。

### 3.代码举例

p93 05:05

## 六、消息过滤



## 七、消息消费重试机制

## 八、死信队列

## 九、消息发送重试机制







# 测试

### 测试1

先提条件：Topic：someTopic（默认4个队列）

- producer（someTopic，tagA）

- ConsumerA1（someTopic，tagA）
- ConsumerA2（someTopic，tagA）

结果：

ConsumerA1消费queueId=0,queueId=3

ConsumerA2消费queueId=1,queueId=2

顺序启动ConsumerA1、ConsumerA2，再启动producer生产10条消息

### 测试2

先提条件：Topic：someTopic（默认4个队列）

- producer（someTopic，tagA）

- ConsumerA1（someTopic，tagA）
- ConsumerA2（someTopic，tagA）
- ConsumerA3（someTopic，tagA）

顺序启动ConsumerA1、ConsumerA2、ConsumerA3，再启动producer生产10条消息

结果：

ConsumerA1消费queueId=2

ConsumerA2消费queueId=3

ConsumerA3消费queueId=0,queueId=1

### 测试3

先提条件：Topic：someTopic（默认4个队列）

- producer1（someTopic，tagA）
- producer2（someTopic，tagB）

- ConsumerA1（someTopic，tagA）
- ConsumerA2（someTopic，tagA）
- ConsumerA3（someTopic，tagB）

顺序启动ConsumerA1、ConsumerA2、ConsumerA3，再启动producer1生产10条消息

结果：

所有消费者均没有收到消息（原因，consumerA3最后启动，将订阅的tag进行了覆盖，此消费者组只消费tagB）

> 再启动一个producer2（someTopic，tagB）
>
> Consumer3消费queueId=3
>
> 其他消费者无消费信息

### 测试4

先提条件：Topic：someTopic（默认4个队列）

- producer1（someTopic，tagA）
- producer2（someTopic，tagB）

- ConsumerA1（someTopic，tagA）
- ConsumerA2（someTopic，tagA）
- ConsumerA3（someTopic，tagB）

顺序启动ConsumerA3、ConsumerA2、ConsumerA1，再启动producer1生产10条消息

结果：

ConsumerA1消费queueId=3

ConsumerA2消费queueId=0,queueId=1

ConsumerA3无消费

> 再启动producer2（someTopic，tagB）
>
> ConsumerA3依旧无消费任何消息
