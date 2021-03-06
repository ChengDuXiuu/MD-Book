## Ribbon是什么

* SpringCloud Ribbon 是基于Netflix Ribbon实现的一套==客户端负载均衡工具==

* 简单的说就是，服务消费者客户端会有⼀个服务器地址列表，调⽤⽅在请求前通过⼀定的负载均衡算法选择⼀个服务器进⾏访问，负载均衡算法的执⾏是在请求客户端进⾏。一般指微服务

* 使用 HttpClient 或 RestTemplate + Eureka注册列表中的微服务ID  来模拟http请求。

	

## Ribbon作用

 将用户的请求平摊到多个服务器节点(冗余)上，从而达到系统的高可用。



* 负载均衡简单分类：

	* 集中式LB

		在服务的消费方和提供方之间使用独立的LB设置，例如Nginx ：反向代理服务器， 由该设施负责把访问的请求通过某种策略转发至服务的提供方。

	* 进程式LB

		* 将LB的逻辑集成到消费方，消费方从服务处测中心获知有哪些可用地址，然后自己再从这些地址中选出一个合适的服务节点。

		* Ribbon就属于进程式LB，他只是一个类库，集成与消费方进程，消费方通过他来获取到服务提供方的地址。

## Ribbon具体流程

![image-20200906174445066](第四章-Ribbon客户端负载均衡.assets/image-20200906174445066.png)



1. 客户端负载均衡**Ribbon**从注册中心Eureka Server中获取服务列表

2. 客户端负载均衡**Ribbon**根据负载均衡算法分发请求

## Spring Cloud集成Ribbon

进过前几章的项目搭建，现项目情况如下：

![image-20200823000653309](第四章-Ribbon客户端负载均衡.assets/image-20200823000653309.png)

> 7001、7002、7003端口对应的微服务为Eureka Server 提供服务注册和发现功能

> 8001端口对应的微服务为服务提供功能，即需要在Eureka Server集群中注册的微服务

> Ribbon主要做客户端的负载均衡，先项目中只有一个客户端即80端口对应的微服务，<font color=ffaa00>因此Ribbon的配置以及开发主要集中于80端口对应的微服务，以下操作若无特殊说明则默认是在80端口对应的微服务中进行操作。</font>

1. 导入依赖

   ```yaml
    <!--Ribbon依赖-->
   <!--        <dependency>-->
   <!--            <groupId>org.springframework.cloud</groupId>-->
   <!--            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>-->
   <!--            <version>1.4.6.RELEASE</version>-->
   <!--        </dependency>-->
    <!--Ribbon依赖于eureka客户端-->
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-eureka</artifactId>
      <version>1.4.6.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.8.5</version>
    </dependency>
   ```

   

2. 编写配置

   ```yaml
   server:
     port: 80
   #Eureka配置
   eureka:
     client:
       register-with-eureka: false # 消费者不需要向Eureka Server中注册自己
       service-url:
         defaultZone: http://eureka01:7001/eureka/,http://eureka02:7002/eureka/,http://eureka03:7003/eureka/ # 集群中进行随机访问
         
   ```

   

3. 开启功能添加注解

   * 启动类中添加

     ```java
     @EnableEurekaClient
     ```

     

   * 在RestTemplate的ConfigBean类中添加Ribbon配置

     > 由于客户端的所有请求都来自于该类的getRestTemplate方法，因此只需要在该方法上进行配置即可。

     * 添加注解

     ```java
     @Configuration//指示一个类声明一个或多个@Bean方法，并且可以由Spring容器处理，以便在运行时为这些bean生成BeanDefinition和服务请求
     public class ConfigBean {//以前是在applicationContext.xml
         @Bean
         @LoadBalanced //Ribbon
         public RestTemplate getRestTemplate(){
             return new RestTemplate();
         }
     }
     ```

     

   * 修改客户端访问地址

     > 之前没有集成Eureka时，客户端是直接通过`服务提供`模块的URL来进行服务访问。而在继承Eureka时，我们讲到，Eureka的作用就是`解决手动维护URL的困境`。因此Eureka相当于一个中间件：服务需要被访问则在Eureka Server中进行注册，客户端需要访问则直接从Eureka中获取即可，`获取的并不是Eureka的URL，而是客户端需要访问的服务的服务名称(服务端配置的spring.application.name)。`

     > 通过服务ID进行访问，由于该服务存在于Eureka集群中，则势必会有一种负载均衡算法(随机、轮训)进行服务`负载均衡调度`

     * 修改 DeptConsumerController 类中REST_URL_PREFIX

       ```java
       //    private static final String REST_URL_PREFIX="http://localhost:8001";//服务提供方固定请求
           private static final String REST_URL_PREFIX="http://SpringCloud-Provider-8001";//和Eureka结合，需使用 spring.application.name
       ```

       

4. 测试

   启动三个Eureka集群、一个服务提供方微服务、一个客户端微服务

   启动客户端报错 

   ```java
   Caused by: java.lang.ClassNotFoundException: org.apache.http.client.HttpClient
   ```

   发现 已包含spring-cloud-starter-netflix-ribbon 2.1.1高版本并且上面引入依赖中重复引入低版本，因此注释掉1.4.6版本的ribbon

   ![image-20200823005401577](第四章-Ribbon客户端负载均衡.assets/image-20200823005401577.png)

   ![image-20200823005534894](第四章-Ribbon客户端负载均衡.assets/image-20200823005534894.png)

   当其中一个Eureka挡掉后，剩余两个Eureka就会补上。并不影响客户的体验。这个时候发现我们访问的永远只有`服务提供`模块，看不出Ribbon负载均衡的作用。因此下面搭建`服务提供`模块集群，来展现Ribbon负载均衡的实际效果。

   

5. 再搭建两个`服务提供`模块

   * 新建两个数据库(springcloud02和springcloud03)用以支撑新建的两个服务模块。

     ```sql
     INSERT INTO `springcloud02`.`dept`(`dname`, `db_source`) VALUES ('开发部', DATABASE());
     INSERT INTO `springcloud02`.`dept`(`dname`, `db_source`) VALUES ('人事部', DATABASE());
     INSERT INTO `springcloud02`.`dept`(`dname`, `db_source`) VALUES ('财务部', DATABASE());
     INSERT INTO `springcloud02`.`dept`(`dname`, `db_source`) VALUES ('市场部', DATABASE());
     INSERT INTO `springcloud02`.`dept`(`dname`, `db_source`) VALUES ('运维部', DATABASE());
     ```

   * 新建两个`服务提供`模块

     > 1、修改yml中的server.port、spring.datasource.url、eureka.instance.instance-id、info.app.name。
     >
     > 2、修改启动类名称。

     > <font color=ff00aa>注意：</font> spring.application.name万万保持一致。具体可参考上面`修改客户端访问地址`

   * 修改三个`服务提供`模块名称
     * 修改 spring.application.name名称由原来的springcloud-provider-8001统一修改为springcloud-provider。
     * 修改REST_URL_PREFIX为springcloud-provider

6. 测试

   * 启动Eureka Server集群、三个服务提供模块、一个客户端模块

     ![image-20200823215319026](第四章-Ribbon客户端负载均衡.assets/image-20200823215319026.png)

     任意一个Eureka服务下均可看到如下所示：

     ![image-20200823215720883](第四章-Ribbon客户端负载均衡.assets/image-20200823215720883.png)

     

   * 负载均衡第一次访问

     ![image-20200823220356325](第四章-Ribbon客户端负载均衡.assets/image-20200823220356325.png)

   * 负载均衡第二次访问

     ![image-20200823220452130](第四章-Ribbon客户端负载均衡.assets/image-20200823220452130.png)

   * 。。。。

   * 内存表示 我还能再战~

     ![image-20200823221316983](第四章-Ribbon客户端负载均衡.assets/image-20200823221316983.png)

## 负载均衡算法

* RoundRobinRule 轮询  (默认)
* RandomRule 随机
* AvailabilityFilteringRule 先过滤掉跳闸访问故障的服务，对剩下可用的进行轮
*  RetryRule 先按照轮询获取服务，如果服务获取失败，则会在指定时间内进行重试

1. 修改默认轮询算法

   在客户端ConfigBean中添加如下代码：

   ```java
   @Bean
       public IRule myRuld(){
           return new RandomRule();
       }
   ```

   