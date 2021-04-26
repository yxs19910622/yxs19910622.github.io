---
title: kafka的参数配置
date: 2020-08-13 11:23:00
tags: kafka
---

Kafka集群的参数配置，应该是使用过程中最重要的，有些配置的重要性并未体现在官方文档中，并且从实际表现看，很多参数对系统的影响要比从文档上看更加明显.

#### Broker端参数

- log.dirs : 肥肠重要的参数, 指定了 Broker 需要使用的若干个文件目录路径

  在线上生产环境中一定要为log.dirs配置多个路径，具体格式是一个 CSV 格式，也就是用逗号分隔的多个路径，比如/data/kafka-logs1,/home/kafka-logs,/home/kafka-logs这样。如果有条件的话你最好保证这些目录挂载到不同的物理磁盘上。这样做有两个好处：

  - 提升读写性能：比起单块磁盘，多块物理磁盘同时读写数据有更高的吞吐量。
  - 能够实现故障转移：即 Failover。这是 Kafka 1.1 版本新引入的强大功能。要知道在以前，只要 Kafka Broker 使用的任何一块磁盘挂掉了，整个 Broker 进程都会关闭。但是自 1.1 开始，这种情况被修正了，坏掉的磁盘上的数据会自动地转移到其他正常的磁盘上，而且 Broker 还能正常工作。

  <!--more-->

- listeners : 告诉外部连接者要通过什么协议访问指定主机名和端口开放的 Kafka 服务。

- advertised.listeners：和 listeners 相比多了个 advertised。Advertised 的含义表示宣称的、公布的，就是说这组监听器是 Broker 用于对外发布的, 主要是为外网访问用的,内网环境可以不配置。

- zookeeper.connect : 这也是一个 CSV 格式的参数，比如我可以指定它的值为zk1:2181,zk2:2181,zk3:2181。2181 是 ZooKeeper 的默认端口。

  如果想让多个 Kafka 集群使用同一套 ZooKeeper 集群，这时候 chroot 就派上用场了。这个 chroot 是 ZooKeeper 的概念，类似于别名。如果你有两套 Kafka 集群，假设分别叫它们 kafka1 和 kafka2，那么两套集群的zookeeper.connect参数可以这样指定：zk1:2181,zk2:2181,zk3:2181/kafka1和zk1:2181,zk2:2181,zk3:2181/kafka2。切记 chroot 只需要写一次，而且是加到最后的。

- auto.create.topics.enable：是否允许自动创建 Topic。建议false,以避免拼写错误产生一些奇怪的Topic

- unclean.leader.election.enable：是否允许 Unclean Leader 选举。不知道默认值的话,最好显示指定false,避免落后进度太多的副本来竞争leader

- auto.leader.rebalance.enable：是否允许定期进行 Leader 选举。生产环境中建议设置false, 因为换leader代价很高, 而且就算原leader一直表现很好,也有可能被强行卸任

- log.retention.{hours|minutes|ms}：这是个“三兄弟”，都是控制一条消息数据被保存多长时间。从优先级上来说 ms 设置最高、minutes 次之、hours 最低。默认log.retention.hours=168是保存7天

- log.retention.bytes：这是指定 Broker 为消息保存的总磁盘容量大小。

- message.max.bytes：控制 Broker 能够接收的最大消息大小。新版中没有了 线上应该设一个比较大的值, 避免消息丢失

#### Topic 级别参数

如果同时设置了 Topic 级别参数和全局 Broker 参数 , Topic 级别参数会覆盖全局 Broker 参数的值，而每个 Topic 都能设置自己的参数值，这就是所谓的 Topic 级别参数

- retention.ms：规定了该 Topic 消息被保存的时长。默认是 7 天，即该 Topic 只保存最近 7 天的消息。一旦设置了这个值，它会覆盖掉 Broker 端的全局参数值。
- retention.bytes：规定了要为该 Topic 预留多大的磁盘空间。和全局参数作用相似，这个值通常在多租户的 Kafka 集群中会有用武之地。当前默认值是 -1，表示可以无限使用磁盘空间。

我们可以在创建或修改 Topic 时进行设置

创建 Topic:

```sh
> bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic transaction --partitions 1 --replication-factor 1 --config retention.ms=15552000000 --config max.message.bytes=5242880
```

修改最大值是 10MB:

```sh
> bin/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --entity-name transaction --alter --add-config max.message.bytes=10485760
```

以上两种方式有些不一样 第二种是自带的命令kafka-configs 未来可能会成为主流

####JVM 参数

Kafka 服务器端代码是用 Scala 语言编写的，但终归还是编译成 Class 文件在 JVM 上运行，因此 JVM 参数设置对于 Kafka 集群的重要性不言而喻。

推荐Java8以上 , JVM堆大小设置成 6GB, 默认的 1GB 有点小

```sh
> export KAFKA_HEAP_OPTS=--Xms6g  --Xmx6g
> export KAFKA_JVM_PERFORMANCE_OPTS= -server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Djava.awt.headless=true
> bin/kafka-server-start.sh config/server.properties
```

#### 操作系统参数

通常情况下，Kafka 并不需要设置太多的 OS 参数，但有些因素最好还是关注一下，比如下面这几个：

- 文件描述符限制 : 通常情况下将它设置成一个超大的值是合理的做法，比如ulimit -n 1000000
- 文件系统类型 : 根据官网的测试报告，XFS 的性能要强于 ext4，所以生产环境最好还是使用 XFS, 也有数据称ZFS貌似性能更加强劲
- Swappiness : 网上很多文章都提到设置其为 0，将 swap 完全禁掉以防止 Kafka 进程使用 swap 空间.但是设置成 0，当物理内存耗尽时，操作系统会触发 OOM killer 这个组件，它会随机挑选一个进程然后 kill 掉，即根本不给用户任何的预警。可以设为1,当开始使用 swap 空间时，你至少能够观测到 Broker 性能开始出现急剧下降，从而给你进一步调优和诊断问题的时间。
- 提交时间(Flush 落盘时间): 可以稍微拉大提交间隔换取性能

