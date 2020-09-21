## 能干啥

之前  多个微服务的请求地址对前台暴露，随意访问

现在  使用Gateway 作为流量的入口。具体参考[第八章-API网关](/Volumes/mac资料/Respository/MD-Book/SpringCloud/第八章-API网关.md)

* 路由转发

* 权限校验

* 限流控制

	

## 相关概念

`路由：`如果在API网关中的断言为真则链接到具体的微服务，否则不进行链接

`断言：`判定过来的请求是否  符合某个路由规则，如果符合则为真

`过滤器：`断言为真时，请求需要经过一系列的过滤器  然后到达对应的服务

![image-20200917212703499](第四章-Gateway(API网关Zuul).assets/image-20200917212703499.png)

## 怎么使用Gateway

如上三个概念。即：

1.  我们如何定义一些`路由规则`
2. 断言是如何判定成功失败
3. 我们该使用哪些Filter

* 路由规则种类：

			* ![image-20200917213050903](第四章-Gateway(API网关Zuul).assets/image-20200917213050903.png)

	 *  例如 范围时间限制  断言

		![image-20200917213625621](第四章-Gateway(API网关Zuul).assets/image-20200917213625621.png)

* 过滤器种类：

	* ![image-20200917213114341](第四章-Gateway(API网关Zuul).assets/image-20200917213114341.png)
	* ![image-20200917213144293](第四章-Gateway(API网关Zuul).assets/image-20200917213144293.png)

	 *  例如 添加请求头  过滤器

		![image-20200917214116350](第四章-Gateway(API网关Zuul).assets/image-20200917214116350.png)

## Spring Cloud整合Gateway

1. 新建项目模块==gulimall-gateway==

2. 嵌入==gulimall-common==模块

	```xml
	<dependency>
	    <groupId>com.shuai.gulimall</groupId>
	    <artifactId>gulimall-common</artifactId>
	    <version>0.0.1-SNAPSHOT</version>
	</dependency>
	```

3. 开启服务的注册发现

	启动类中添加：

	```java
	@EnableDiscoveryClient//开启服务注册发现
	```

4. 配置文件中配置Nacos注册地址

	```yaml
	# application.yml
	#nacos注册
	spring:
	  cloud:
	    nacos:
	      discovery:
	        server-addr: 127.0.0.1:8848
	  application:
	    name: service-provider-gateway
	
	server:
	  port: 8888
	```

5. 使用配置中文管理配置

	* 新建微服务命名空间

		![image-20200917215810447](第四章-Gateway(API网关Zuul).assets/image-20200917215810447.png)

	* 新建配置文件

		![image-20200917215748937](第四章-Gateway(API网关Zuul).assets/image-20200917215748937.png)



6. 启动测试

	失败：

	​		因为引入了common  而common中有数据源的依赖，spring boot会自动加载配置属性。

	解决：

	​		引入common模块时将mybatis排除

	```java
	@EnableDiscoveryClient//开启服务注册发现
	@SpringBootApplication(exclude = DataSourceAutoConfiguration.class) // 将数据库相关自动配置排除
	public class GulimallGatewayApplication {
	
	    public static void main(String[] args) {
	        SpringApplication.run(GulimallGatewayApplication.class, args);
	    }
	
	}
	
	```

7. 添加路由规则

	```yml
	application.yml
	#nacos注册
	spring:
	  cloud:
	    nacos:
	      discovery:
	        server-addr: 127.0.0.1:8848
	    gateway:
	      routes: # 使用官网提供的 5.9 Query Route Predicate Factory  路由规则
	      - id: query_route_baidu # 路由给百度
	        uri: https://www.baidu.com
	        predicates:
	        - Query=url, baidu  # URL中包含百度则路由给百度
	      - id: query_route_QQ # 路由给QQ
	        uri: https://www.qq.com
	        predicates:
	        - Query=url, qq
	
	
	
	  application:
	    name: service-provider-gateway
	
	server:
	  port: 8888
	
	```

8. 测试

	* 浏览器访问 http://localhost:8888/hello?url=qq

		![image-20200917222015749](第四章-Gateway(API网关Zuul).assets/image-20200917222015749.png)

		