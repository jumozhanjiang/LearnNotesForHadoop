### 为什么需要消息队列

* 解耦
  * 允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。
* 冗余
  * 消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的"插入-获取-删除"范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。
* 扩展性
  * 因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。
* 灵活性和峰值处理能力
  * 在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。
* 可恢复性
  * 系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。
* 顺序保证
  * 在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。（Kafka保证一个Partition内的消息的有序性）
* 缓冲
  * 有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。
* 异步通信
  * 很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

### 什么是Kafka

> 在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。

* Apache Kafka是一个开源消息系统，由Scala写成。是由Apache软件基金会开发的一个开源消息系统项目。
* Kafka最初是由LinkedIn公司开发，并于2011年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。
* Kafka是一个分布式消息队列。Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer，消息接受者称为Consumer，此外kafka集群有多个kafka实例组成，每个实例(server)称为broker。
* 无论是kafka集群，还是consumer都依赖于zookeeper集群保存一些meta信息，来保证系统可用性。

### kafka架构

![](./img/image01.jpg)

* Producer
  * 消息生产者，就是向kafka broker发消息的客户端
* Consumer
  * 消息消费者，向kafka broker取消息的客户端
* Topic
  * 可以理解为一个队列
* Consumer Group(GG)
  * 这是kafka用来实现一个topic消息的广播（发给所有的consumer）和单播（发给任意一个consumer）的手段。一个topic可以有多个CG。topic的消息会复制（不是真的复制，是概念上的）到所有的CG，但每个partion只会把消息发给该CG中的一个consumer。如果需要实现广播，只要每个consumer有一个独立的CG就可以了。要实现单播只要所有的consumer在同一个CG。用CG还可以将consumer进行自由的分组而不需要多次发送消息到不同的topic
* Broker
  * 一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic
* Partition
  * 为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序
* Offset
  * kafka的存储文件都是按照offset.kafka来命名，用offset做名字的好处是方便查找。例如你想找位于2049的位置，只要找到2048.kafka的文件即可。当然the first offset就是00000000000.kafka


### kafka分布式部署

> [下载地址](http://archive.apache.org/dist/kafka/)

> 集群规划

hadoop01 | hadoop02 | hadoop03 | hadoop04
:--: | :--: | :--: | :--:
zk | zk | zk | zk
kafka | kafka | kafka | kafka

> <font style="color:red">搭建zookeeper集群请参考我的对应文章</font>

* 解压`kafka压缩包`

* 修改配置文件
  * `vi ./config/server.properties`
    ```
    #broker的全局唯一编号，不能重复                                   * 需要修改
    broker.id=1
    #处理网络请求的线程数量
    num.network.threads=3
    #用来处理磁盘IO的现成数量
    num.io.threads=8
    #发送套接字的缓冲区大小
    socket.send.buffer.bytes=102400
    #接收套接字的缓冲区大小
    socket.receive.buffer.bytes=102400
    #请求套接字的缓冲区大小
    socket.request.max.bytes=104857600
    #kafka运行日志存放的路径	                                      * 需要修改
    log.dirs=/opt/module/kafka/logs
    #topic在当前broker上的分区个数
    num.partitions=1
    #用来恢复和清理data下数据的线程数量
    num.recovery.threads.per.data.dir=1
    #segment文件保留的最长时间，超时将被删除
    log.retention.hours=168
    #配置连接Zookeeper集群地址                                        * 需要修改
    zookeeper.connect=hadoop102:2181,hadoop103:2181,hadoop104:2181
    ```

* 配置环境变量
  * `vi ~/.bashrc`
    ```
    #KAFKA_HOME
    export KAFKA_HOME=/home/yetao_yang/elastic/kafka/kafka-2.2.0
    export PATH=$PATH:$KAFKA_HOME/bin
    ```
  * 刷新环境变量
    * `source ~/.bashrc`
* 把`kafka`传到其它的机器上面,并修改`broker.id`参数,以及配置环境变量
* 启动zookeeper集群(看zookeeper文档)
* 启动kafka集群(每个节点上面都执行一遍)
  * `nohup bin/kafka-server-start.sh config/server.properties &`
* 查看进程
  ```shell
  [yetao_yang@hadoop01 kafka-2.2.0]$ jps
  7761 Kafka
  6966 QuorumPeerMain
  8157 Jps
  ```
