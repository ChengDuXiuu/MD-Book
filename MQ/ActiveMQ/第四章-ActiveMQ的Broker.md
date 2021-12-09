## 是什么？

之前使用ActiveMQ的时候，都是需要Linux服务器去下载tar然后启动ActiveMQ服务

而Broker就是将服务程序Java化，我们可以通过启动activemq的jar包的方式来启动ActiveMQ的服务。





## 启动多个实例

类似于之前的Redis，通过修改config文件，来启动Redis集群，ActiveMQ也可以通过这种方式来达到集群。其配置文件为activemq.xml





## broker

```java
public class EmbedBroker {
    @SneakyThrows
    public static void main(String[] args) {
        BrokerService brokerService = new BrokerService();
        brokerService.setUseJmx(Boolean.TRUE);
        brokerService.addConnector("tcp://localhost:61616");
        brokerService.start();
    }
}
```

