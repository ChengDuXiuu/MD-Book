## 分布式系统面临的问题





## 服务雪崩





## 什么是Hystrix

![image-20200825204350390](第六章-Hystrix服务熔断.assets/image-20200825204350390.png)







## SpringCloud集成Hystrix

### 服务熔断(服务器端)

​	熔断机制是对应雪崩效应的一种微服务链路保护机制。当微服务调用链中某个微服务不可用或者响应时间太长时，会进行服务的降级，==不再继续调用目标服务，快速返回错误的响应信息==。当检测到该节点为服务调用响应正常后恢复调用链路。

1. 新建Hystrix项目模块 `SpringCloud-Provider-Hystrx-8008`

   > <font color=ff00aa>说明：</font> 该模块主要是用来和之前的三个`服务提供`模块作对比。之前的服务模块并没有提供服务熔断机制，当掉就是真的当掉了，一定会会造成服务雪崩的场景。

2. 导入依赖

   ```xml
   <!--hystrix-->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-hystrix</artifactId>
     <version>1.4.6.RELEASE</version>
   </dependency>
   ```

   

3. 修改yml配置
   
* 修改server.port、instance.instance-id、info.app.name
  
4. 修改DeptController类

   ```java
   @RestController//提供restful风格服务
   public class DeptController {
       @Autowired
       private DeptService deptService;
   
       @GetMapping("/dept/get/{id}")
       @HystrixCommand(fallbackMethod = "hystrixGet")//只要该方法获取失败则调用hystrixGet方法
       public Dept queryById(@PathVariable("id") long id) {
           Dept dept = deptService.queryById(id);
           // todo 模拟服务中断或者超时情况  然后使用Hystrix提供的服务备选功能代替死掉的服务
           if (null==dept){
               throw new RuntimeException("id "+id+"用户并不存在，或者无法找到该用户。");
           }
           return dept;
       }
       //服务备选方法
       public Dept hystrixGet(@PathVariable("id") long id){
           return new Dept(id,"id "+id+"用户并不存在，或者无法找到该用户。","No This Database in mYSQL");
       }
   
       @PostMapping("/dept/add ")
       public boolean addDept(@RequestBody Dept dept){
           return deptService.addDept(dept);
       }
       @GetMapping("/dept/list")
       public List<Dept> queryAll() {
           return deptService.queryAll();
       }
   
       @Autowired
       private DiscoveryClient client;
       //获取注册进来的微服务信息
       @GetMapping("/dept/discovery")
       public Object discovery(){
           List<String> services = client.getServices();
           System.out.println("discovery=>services:"+services);
           services.forEach(x -> System.out.println(x.toString()));
           return this.client;
       }
   
   }
   ```

   

5. 修改启动类添加熔断支持注解

   ```java
   @SpringBootApplication
   @EnableEurekaClient//自动将此服务加入到Eureka Server中
   @EnableDiscoveryClient//服务发现
   @EnableCircuitBreaker//添加熔断支持
   public class HystrxDeptProvider_8008 {
       public static void main(String[] args) {
           SpringApplication.run(HystrxDeptProvider_8001.class, args);
       }
   }
   ```

   

6. 测试

   启动三个Eureka Server服务集群、四个`服务提供`微服务【其中一个提供熔断机制】、一个Feign-8000微服务、一个Ribbon-80微服务。

   ![image-20200824215142152](第六章-Hystrix服务熔断.assets/image-20200824215142152.png)

   ![image-20200824215646075](第六章-Hystrix服务熔断.assets/image-20200824215646075.png)

   访问ID为1的用户正常访问

   ![image-20200824220511679](第六章-Hystrix服务熔断.assets/image-20200824220511679.png)

   访问ID不存在的用户则走我们定义好的@HystrixCommand(fallbackMethod = "hystrixGet")方法

   ![image-20200824220559997](第六章-Hystrix服务熔断.assets/image-20200824220559997.png)

7. 修改显示名称

   > 在Eureka Server 服务页面中我们点击服务，浏览器左下角显示服务地址为Localhost，修改显示为IP地址

   ![image-20200824221827611](第六章-Hystrix服务熔断.assets/image-20200824221827611.png)

   在四个`服务提供`微服务的yml中添加

   ```
   eureka..instance.prefer-ip-address: true #默认显示localhost 修改后显示服务IP地址
   ```

   ![image-20200824222119492](第六章-Hystrix服务熔断.assets/image-20200824222119492.png)

### 服务降级(客户端)

当下游的服务因为某种原因响应过慢，下游服务主动停掉一些不太重要的业务或者调用上游本地的一些降级逻辑，释放出服务器资源，增加响应速度。

例如双十一淘宝众某些模块服务不可用。

* SpringCloud-API模块新增类

  ```java
  //降级代码
  @Component
  public class DeptClientServiceFallbackFactory implements FallbackFactory {
      @Override
      public Object create(Throwable throwable) {
          return new DeptClientService() {
              @Override
              public Dept queryById(Long id) {
                  return new Dept(id,"id "+id+"用户并不存在，或者无法找到该用户。","No This Database in mYSQL");
              }
  
              @Override
              public List<Dept> queryAll() {
                  return null;
              }
  
              @Override
              public Boolean addDept(Dept dept) {
                  return null;
              }
          };
      }
  }
  ```

* DeptClientService接口新增参数调用

  ```java
  @FeignClient(value = "SPRINGCLOUD-PROVIDER",fallbackFactory = DeptClientServiceFallbackFactory.class) //服务名称 与Ribbon中REST_URL_PREFIX一致。通过服务名称获取服务
  @Component
  public interface DeptClientService {
      @GetMapping("/dept/get/{id}")
      public Dept queryById(Long id);
      @GetMapping("/dept/list")
      public List<Dept> queryAll();
      @GetMapping("/dept/add")
      public Boolean addDept(Dept dept);
  }
  ```



* 客户端模块新增配置

  ```yaml
  #开启Feign.hystrix
  feign:
    hystrix:
      enabled: true
  ```

* 启动客户端(消费模块)提示找不到DeptClientServiceFallbackFactory

  > 在客户端启动类添加扫描组件@ComponentScan("com.shuai") //扫描API模块

