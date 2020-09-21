## Spring Cloud整合Nacos

2. 下载nacos

3. bin目录下启动，Mac｜Linux默认为集群默认，启动加（-m standalone）改为单机模式

	```bash
	sh startup.sh -m standalone
	```

3. ![image-20200915232432274](/Users/mac/Library/Application Support/typora-user-images/image-20200915232432274.png)

4. 登录页面：http://127.0.0.1:8848/nacos/index.html
5. 默认账号密码
	- 账号：nacos
	- 密码：nacos

6. 主界面

	![image-20200915232537215](/Users/mac/Library/Application Support/typora-user-images/image-20200915232537215.png)

8. <加入依赖包>修改 pom.xml 文件(==服务注册方==或者==common模块==)，引入 Nacos Discovery Starter。

	```xml
	<dependency>
	     <groupId>com.alibaba.cloud</groupId>
	     <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
	 </dependency>
	```

9. <提供配置文件或者配置类>在==服务提供方==的 application.yml配置文件中配置 Nacos Server 地址以及==服务名==（对外暴露使用）

	```yaml
	spring:
	  cloud:
	    nacos:
	      discovery:
	        server-addr: 127.0.0.1:8848
	  application:
	    name: service-provider-coupon      
	```

10. <开启功能>使用 @EnableDiscoveryClient 注解开启服务注册与发现功能

	```java
	@SpringBootApplication
	@EnableDiscoveryClient
	public class Application {
	
	    public static void main(String[] args) {
	        SpringApplication.run(GulimallCouponApplication.class, args);
	    }
	
	}
	```

11. 测试

	* 启动Nacos中间件。类似于Eureka Server服务模块
	* 启动==服务提供方==模块

	![image-20200916202356225](第一章-Nacos(Eureka服务注册发现).assets/image-20200916202356225.png)

## 与Eureka区别

Eureka两大组件`Eureka Server`和`Eureka Client`都需要自己提供实现模块。

Nacos已提供好注册服务脚本以及可视化界面，只需要往里面注册服务即可