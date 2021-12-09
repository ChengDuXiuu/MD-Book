# kafka工作流程

![image-20211209175314552](第二章-kafka高级.assets/image-20211209175314552.png)

> * Kafka 中消息是以 topic 进行分类的， 生产者生产消息，消费者消费消息，都是面向 topic的
>
> * 如上，kafka有三个broker ，表名三个服务器组成kafka集群。
> * kafka集群如何进行数据备份的？三个服务器，每个服务器中都有leader和follower，但是follower并不备份同一个服务器中leader的数据。如果备份了，这台服务器宕机，那么主从数据都丢失了。
> * topic 是逻辑上的概念，而 partition 是物理上的概念，在磁盘中存储格式为topic-分区号，每个 partition 对应于一个 log 文件，该 log 文件中存储的就是 producer 生产的数据。 Producer 生产的数据会被不断追加到该
> 	log 文件末端，且每条数据都有自己的 offset。 消费者组中的每个消费者， 都会实时记录自己消费到了哪个 offset，以便出错恢复时，从上次的位置继续消费。因此kafka可以保证一个区内生产和消费数据有序性，整个集群中就不能保证了。

1. 生产者创建了一个topic三个分区 【TopicA-partition0、TopicA-partition1、TopicA-partition2】
2. 三个分区落在三个服务器上，每个服务器相互存储其他服务器leader的备份数据
3. 消费者消费数据从leader中根据偏移量进行数据消费



* kafka对应topic数据存储形式

	![image-20211209181031514](第二章-kafka高级.assets/image-20211209181031514.png)

	> 因为这里只有一个分区，即一台服务器，默认分区为0。
	>
	> 创建了三个主题，对应的文件夹就是 topic-分区号。如上。

* 每个topic 对应文件夹有如下内容

	![image-20211209181219853](第二章-kafka高级.assets/image-20211209181219853.png)

	> .index 文件 是偏移量，记录了消费者消费数据的偏移量，方便下次继续消费
	>
	> .Log 文件 是存储的数据



# kafka文件存储机制

> 上面我们说到了：
>
> * kafka 在磁盘上以 topic-分区号 进行存储数据的
> * 每个 topic 文件夹中最起码有两类文件.index和.log。其中.index是偏移量文件、.log是存储数据的文件



![image-20211209181753597](第二章-kafka高级.assets/image-20211209181753597.png)

> 一个topic 可以有多个分区文件或者一个分区文件
>
> 每个分区文件以 topic-分区号命名，旗下有两类文件。
>
> 由于生产者生产的消息是不断追加到log文件末尾，为防止 log 文件过大导致数据定位效率低下， Kafka 采取了**分片和索引**机制，将每个 partition 分为多个 segment。 segment文件默认1G存储容量，每个 segment对应两个文件——“.index”文件和“.log”文件。 这些文件位于一个文件夹下， 该文件夹的命名规则为： topic 名称+分区序号。例如， first 这个 topic 有三个分区，则其对应的文件夹为 first-0,first-1,first-2。



## idnex和log文件详解

> 上面我们得知：Kafka 采取了**分片和索引**机制，每个segment 都有index和log文件。并且每个segment就是采用了分片机制，那索引机制体现在哪里呢？体现在文件命名上，如下：

![image-20211209182312479](第二章-kafka高级.assets/image-20211209182312479.png)

> 文件命名中第一个segment的index和log是000000000000000.xxx 。 之后index 和 log 文件以当前 segment 的第一条消息的 offset 命名。
>
> 即文件名中的数字就是该文件的起始索引。例如000000000000170410.index，表明该文件中数据起步索引为170410。

![image-20211209182652037](第二章-kafka高级.assets/image-20211209182652037.png)

> idnex 文件中两列数据，第一列保存的是索引，第二列保存的是索引对应的log文件中的数据偏移量



## 如何查找 偏移量为3的数据？

> 我们习惯性的会以为，它会先扫描所有index文件找到索引对应的数据偏移量，然后去log文件中获取对应的数据。其实不是。
>
> 因为每个index 保存的是简单且固定容量的数据，假如每行设定1k大小，如果要找 索引为3 的数据，根据【文件名记录的起步索引和文件容量/1K】判定index=3的数据在哪个文件，又因为每行容量1K，可以直接定位到index=3对应的行。省去了遍历文件的慢速度。



# 生产者内容



## 分区策略

### 分区原因

1. 方便在集群中扩展，每个 Partition 可以通过调整以适应它所在的机器，而一个 topic又可以有多个 Partition 组成，因此整个集群就可以适应任意大小的数据了；
2. 可以提高并发，因为可以以 Partition 为单位读写了。



### 分区原则

我们需要将 producer 发送的数据封装成一个 ProducerRecord 对象，提供了如下构造函数

![image-20211209184648726](第二章-kafka高级.assets/image-20211209184648726.png)

* 指明 partition 的情况下，直接将指明的值直接作为 partiton 值，将数据存储在该分区。
* 没有指明 partition 值但有 key 的情况下，将 key 的 hash 值与 topic 的 partition数进行取余得到 partition 值；
* 既没有 partition 值又没有 key 值的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上自增），将这个值与 topic 可用的 partition 总数取余得到 partition值，也就是常说的 round-robin 算法



### 数据可靠性保证

> 如何确保生产者生产的数据确保到达topic？ActiveMQ是通过【事务、签收、事务==》统一概述为异步投递+回调  |  同步投递】

kafka为保证 producer 发送的数据，能可靠的发送到指定的 topic， topic 的每个 partition 收到producer 发送的数据后， **都需要向 producer 发送 ack（acknowledgement 确认收到）** ，如果producer 收到 ack， 就会进行下一轮的发送，否则重新发送数据。

![image-20211209203111647](第二章-kafka高级.assets/image-20211209203111647.png)

![image-20211209203452043](第二章-kafka高级.assets/image-20211209203452043.png)

> Kafka 选择了第二种方案，原因如下：
> 1.同样为了容忍 n 台节点的故障，第一种方案需要 2n+1 个副本，而第二种方案只需要 n+1个副本，而 Kafka 的每个分区都有大量的数据， 第一种方案会造成大量数据的冗余。
> 2.虽然第二种方案的网络延迟会比较高，但网络延迟对 Kafka 的影响较小。



### 第二种方案引发的问题

发送ack消息得确保所有follower同步完成，但是如果有一台follower因为某种故障导致迟迟不能同步，那么ack就无法回复，导致生产者发送数据到topic阻塞。



### 解决方案:ISR

​		Leader 维护了一个动态的 in-sync replica set (ISR)，意为和 leader 保持同步的 follower 集合。当 ISR 中的 follower 完成数据的同步之后， leader 就会给 follower 发送 ack。如果 follower长 时 间 未 向 leader 同 步 数 据 ， 则 该 follower 将 被 踢 出 ISR ， 该 时 间 阈 值 由replica.lag.time.max.ms 参数设定。 Leader 发生故障之后，就会从 ISR 中选举新的 leader。



### ack应答机制

对于某些不太重要的数据，对数据的可靠性要求不是很高，能够容忍数据的少量丢失，所以没必要等 ISR 中的 follower 全部接收成功。所以 Kafka 为用户提供了三种可靠性级别，用户根据对可靠性和延迟的要求进行权衡，选择以下的配置。

* 0： producer 不等待 broker 的 ack，相当于异步投递。这一操作提供了一个最低的延迟， broker 一接收到还没有写入磁盘就已经返回，当 broker 故障时有可能**丢失数据**；
* 1： producer 等待 broker 的 ack， partition 的 leader 落盘成功后返回 ack，相当于同步投递，如果在 follower同步成功之前 leader 故障，那么将**会丢失数据**；

* -1（all） ： producer 等待 broker 的 ack， partition 的 leader 和 follower 全部落盘成功后才返回 ack。但是如果在 follower 同步完成后， broker 发送 ack 之前， leader 发生故障，那么会造成**数据重复**。



## 数据一致性问题

### 触发原因

当leader挂掉后，重新选举leader，但是所有的follower中数据都少于leader数据，那么就会出现数据不同步情况。

> 虽然kafka选取了【leader和follower全部同步后进行ack应答，但是ack应答机制中如果不设置为-1，还是会出现数据丢失的情况】，从而造成leader和follower数据不同步情况



### 解决办法 HW和LEO

![image-20211209205853277](第二章-kafka高级.assets/image-20211209205853277.png)

> LEO:leader中数据文件最大索引为19，这个19 就是LEO 即leader的log文件最大偏移量
>
> HW: **ISR 队列**中最小的 LEO最小的LEO，上图中，有两个副本，且LEO分别对应为12和15.其中最小的为HW

* follower 故障
	follower 发生故障后会被临时踢出 ISR，待该 follower 恢复后， follower 会读取本地磁盘记录的上次的 HW，并将 log 文件高于 HW 的部分截取掉，从 HW 开始向 leader 进行同步。等该 follower 的 LEO 大于等于该 Partition 的 HW，即 follower 追上 leader 之后，就可以重新加入 ISR 了。
* leader 故障
	leader 发生故障之后，会从 ISR 中选出一个新的 leader，之后，为保证多个副本之间的数据一致性， 其余的 follower 会先将各自的 log 文件高于 HW 的部分截掉，然后从新的 leader同步数据。

<font color=ff00aa size=4>注意： 这只能保证副本之间的数据一致性，并不能保证数据不丢失或者不重复</font>



## Exactly Once



* At Least Once 至少一次

​		将服务器的 ACK 级别设置为-1，可以保证 Producer 到 Server 之间**不会丢失数据，但是不能保证数据不重复**

* At Most Once 至多一次

	将服务器 ACK 级别设置为 0，可以保证生产者每条消息只会被发送一次，**保证数据不重复，但是不能保证数据不丢失**

> 上面两种办法都无法达到**精准一次** 即一次投递一次消费。kafka如何解决的呢？
>
> 0.11 版本的 Kafka，引入了一项重大特性：幂等性。所谓的幂等性就是指 Producer 不论向 Server 发送多少次重复数据， Server 端都只会持久化一条。幂等性结合 At Least Once 语义，就构成了 Kafka 的 Exactly Once 语义。即：**At Least Once + 幂等性 = Exactly Once**



### 如何开启Exactly Once

要启用幂等性，只需要将 Producer 的参数中 enable.idompotence 设置为 true 即可，设置为true后 ack 默认就是 -1。 Kafka的幂等性实现其实就是将原来下游需要做的去重放在了数据上游。开启幂等性的 Producer 在初始化的时候会被分配一个 PID，发往同一 Partition 的消息会附带 Sequence Number。而Broker 端会对<PID, Partition, SeqNumber>做缓存，当具有相同主键的消息提交时， Broker 只会持久化一条。

> 但是 PID 重启就会变化，同时不同的 Partition 也具有不同主键，所以幂等性无法保证跨分区跨会话的 Exactly Once。 





## 生产者总结

`如何保证数据可靠性？`

1. 提供类似于ActiveMQ的同步投递，等待leader节点将消息持久化后进行ACK应答
2. ACK应答牵扯到什么时候应答？即主从数据**全部同步完**应答还是**同步一半**就应答
3. kafka选用了**全部同步**后进行ACK应答，这样虽然提高了延时但是能够保证数据的可靠性。
4. ACK应答机制中，kafka提供了三种选择：为0->不等主从同步即返回ACK，为1->等leader持久化消息后返回ACK，为-1->等待所有从节点完成后再返回ACK。但是这三种都有问题，第一二种会造成**数据丢失**，第三种造成**数据重复**。如何完美解决呢？
5. 使用**第三种应答机制+幂等性==>Exactly Once**，但是只能保证**同区内数据可靠性**。



# 消费者

## 消费方式

consumer 采用 pull 模式(主动轮询)从 broker 中读取数据。push模式(broker主动推送)很难适应消费速率不同的消费者，因为消息发送速率是由 broker 决定的。它的目标是尽可能以最快速度传递消息，但是这样很容易造成 consumer 来不及处理消息，典型的表现就是**拒绝服务以及网络拥塞。而 pull 模式则可以根据 consumer 的消费能力以适当的速率消费消息。**

pull 模式不足之处是，如果 kafka 没有数据，消费者可能会陷入循环中， 一直返回空数据。 针对这一点， Kafka 的消费者在消费数据时会传入一个时长参数 timeout，如果当前没有数据可供消费， consumer 会等待一段时间之后再返回，这段时长即为 timeout。



## 分区分配策略

一个 consumer group 中有多个 consumer，一个 topic 有多个 partition，所以必然会涉及到 partition 的分配问题，即确定那个 partition 由哪个 consumer 来消费。Kafka 有两种分配策略，一是 RoundRobin，一是 Range(默认采用此种方式，采用主题进行分配)。



## offset 的维护

> 由于 consumer 在消费过程中可能会出现断电宕机等故障， consumer 恢复后，需要从故障前的位置的继续消费，所以 consumer 需要实时记录自己消费到了哪个 offset，以便故障恢复后继续消费。



## kafka 消费者组案例







## 高效读写数据

* 顺序写磁盘
	Kafka 的 producer 生产

* 数据，要写入到 log 文件中，写的过程是一直追加到文件末端，为顺序写。 官网有数据表明，同样的磁盘，顺序写能到 600M/s，而随机写只有 100K/s。这
	与磁盘的机械机构有关，顺序写之所以快，是因为其省去了大量磁头寻址的时间。

* 零复制技术

	![image-20211209214942708](第二章-kafka高级.assets/image-20211209214942708.png)

	> 正常来讲，我们代码都是通过操作系统去进行文件读写，零复制技术就是，代码通过命令直接让操作系统进行文件复制拷贝即可，

​	

## ZooKeeper在kafka中额作用

Kafka 集群中有一个 broker 会被选举为 Controller，负责管理集群 broker 的上下线，所有 topic 的分区副本分配和 leader 选举等工作。Controller 的管理工作都是依赖于 Zookeeper 的。以下为 partition 的 leader 选举过程：

![image-20211209215320825](第二章-kafka高级.assets/image-20211209215320825.png)



# kafka事务

Kafka 从 0.11 版本开始引入了事务支持。事务可以保证 Kafka 在 Exactly Once 语义的基础上，生产和消费可以跨分区和会话，要么全部成功，要么全部失败。



## Producer 事务
为了实现跨分区跨会话的事务，需要引入一个全局唯一的 Transaction ID，并将 Producer获得的PID 和Transaction ID 绑定。这样当Producer 重启后就可以通过正在进行的 Transaction
ID 获得原来的 PID。为了管理 Transaction， Kafka 引入了一个新的组件 Transaction Coordinator。 Producer 就是通过和 Transaction Coordinator 交互获得 Transaction ID 对应的任务状态。 TransactionCoordinator 还负责将事务所有写入 Kafka 的一个内部 Topic，这样即使整个服务重启，由于事务状态得到保存，进行中的事务状态可以得到恢复，从而继续进行。



## Consumer 事务
上述事务机制主要是从 Producer 方面考虑，对于 Consumer 而言，事务的保证就会相对较弱，尤其时无法保证 Commit 的信息被精确消费。这是由于 Consumer 可以通过 offset 访
问任意信息，而且不同的 Segment File 生命周期不同，同一事务的消息可能会出现重启后被删除的情况。



# API

## Producer API

Kafka 的 Producer 发送消息采用的是**异步发送**的方式。在消息发送的过程中，涉及到了两个线程——**main 线程和 Sender 线程**，以及一个线程共享变量——RecordAccumulator。**main 线程将消息发送给 RecordAccumulator， Sender 线程不断从 RecordAccumulator 中拉取消息发送到 Kafka broker。**

> <font color=ff00aa size=4>前面我们讲到了ACK，并且简单理解为ACK就是当前一个消息发送完并接收到broker返回的ACK才进行下一个消息的发送，并不是这样的，而是异步投递，当A消息发送后并不会阻塞等待ACK返回，而是直接发送下一个消息，如果之前消息的ACK一直没有返回触发重试机制。和ActiveMQ的ACK还是有区别的</font>



### 发送流程

![image-20211209220514020](第二章-kafka高级.assets/image-20211209220514020.png)

`相关参数：`

* batch.size： 只有数据积累到 batch.size 之后， sender 才会发送数据。
* linger.ms： 如果数据迟迟未达到 batch.size， sender 等待 linger.time 之后就会发送数据。



### 代码案例

> 刚开始连接一直是连接的本地kafka，去服务器修改server.properties ,添加如下内容
>
> port=9092
>
> //外网IP
>
> advertised.host.name=192.168.31.98

```java
    public static void main(String[] args) {
        //创建kafka生产者配置信息
        Properties properties = new Properties();
        //连接kafka
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.31.98:9092");
        //ack应答级别
        properties.put(ProducerConfig.ACKS_CONFIG, "all");
        //重试次数
        properties.put(ProducerConfig.RETRIES_CONFIG, 1);
        //批次大小
        properties.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
        //等待时间
        properties.put(ProducerConfig.LINGER_MS_CONFIG, 1);
        //RecordAccumulator缓冲区的大小
        properties.put(ProducerConfig.BATCH_SIZE_CONFIG, 33554432);
        //Key,Value的序列化类
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");

        Producer<String, String> producer = new KafkaProducer<>(properties);
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("first", "key--"+i, "value--"+i), new Callback() {
                @Override
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    //成功返回recordMetadata，失败返回Exception
                    if (e == null) {
                        System.out.println("topic: " + recordMetadata.topic());
                    }
                }
            });
        }
        producer.close();
    }
```

命令启动消费者，收到如下消息：

![image-20211209230652079](第二章-kafka高级.assets/image-20211209230652079.png)



> 代码如下：https://gitee.com/gadeGG/ProjectCode/tree/master/SpringBootLearn/Kafka-Product

## Consumer API