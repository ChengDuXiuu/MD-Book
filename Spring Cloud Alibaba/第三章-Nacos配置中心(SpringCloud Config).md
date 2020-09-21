### Spring Cloud整合Nacos配置中心

`作用：`以前在Spring Boot任何方法从配置文件中获取值，都可以使用配置中心代替并集中式管理

​			包括@Value、@ConfigurationProperties。。。

`原则：`配置中心优先级高于服务中的配置文件

1. 首先，修改 pom.xml 文件，引入 Nacos Config Starter。

	```xml
	 <dependency>
	     <groupId>com.alibaba.cloud</groupId>
	     <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
	 </dependency>
	```

2. 在应用的 /src/main/resources/bootstrap.properties 配置文件中配置 Nacos Config 元数据

	```properties
	 spring.application.name=nacos-config-example
	 spring.cloud.nacos.config.server-addr=127.0.0.1:8848
	```

	> 文件说明可参考 [第九章-分布式配置中心](/Volumes/mac资料/Respository/MD-Book/SpringCloud/第九章-分布式配置中心.md)

3. 之前Spring Bean注入时可以通过@Value动态获取配置文件中内容

	* 新增配置文件

		```properties
		# application.properties
		user.name=张三
		user.age=18
		```

	* 获取配置文件内容

	```java
	@RefreshScope
	@RestController
	@RequestMapping("gulimallcoupon/coupon")
	public class CouponController {
	    @Autowired
	    private CouponService couponService;
	
	    @Value("${user.name}")
	    private String name;
	    @Value("${user.age}")
	    private Integer age;
	    @RequestMapping("/getConfig")
	    public R getConfig(){
	        return R.ok().put("name : ", name).put("age : ", age);
	    }
	}  
	```

	* 测试

		![image-20200916224408997](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916224408997.png)

> 原先方式并不能动态的修改值，如果修改后需要重新部署。Nacos配置中心可以帮我们实现配置文件中心管理以及动态生效的效果。类似于Spring Cloud Config

4. Nacos配置管理

	* 项目启动时可以发现Nacos默认去从`service-provider-coupon.properties`读取信息

		![image-20200916225205580](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916225205580.png)

	* `service-provider-coupon.properties`这个名字就是我们==服务名==

	* ![image-20200916225535985](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916225535985.png)

	* ![image-20200916225636029](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916225636029.png)

		> 配置内容中写`步骤3`中新增的application.properties文件内容  发布即可

	

	* controller类中新增注解==@RefreshScope==

		> 如 步骤3 中「获取配置文件内容」

5. 测试

	* 启动Nacos注册服务 修改内容

		![image-20200916230422774](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916230422774.png)

	* 启动模块服务

		![image-20200916230540676](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200916230540676.png)

	> 这样做的一个好处 就是可以将配置文件管理从众多微服务配置中剥离出来，并且可以直接交由运营小伙伴管理。

	> 并且可以像  [第九章-分布式配置中心](/Volumes/mac资料/Respository/MD-Book/SpringCloud/第九章-分布式配置中心.md)将所有微服务的配置文件放置在Nacos服务中。Spring Cloud Config配置文件放置在了Git中

## Nacos配置中心命名空间与配置分组

`命名空间`：给配置文件做配置隔离：多个环境下互不干扰 | 每个微服务配置文件隔离

![image-20200917201839044](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917201839044.png)

* 新建命名空间

	![image-20200917202033527](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917202033527.png)

* 在test命名空间中增加配置文件

	![image-20200917202411294](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917202411294.png)

* 选择`test`命名空间下配置文件

	* 获取命名空间ID

		![image-20200917202732729](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917202732729.png)

	* bootstrap.properties中新增

		```prop
		#bootstrap.properties
		spring.cloud.nacos.config.namespace=3d640f27-9ea9-41be-93b2-d394181f35f6
		```

> 可以为每个微服务创建命名空间，互不干扰，与上面的操作一样。这里就不做演示了。

`配置集`：所有配置的集合

`配置集合ID`：类似配置文件名  即新建配置时中的Data id

`配置分组`：默认所有的配置集都属于 DEFAULT_GROUP

> 命名空间下又可以分组，即不同的微服务使用不同的命名空间，同一个微服务下使用不同的环境

![image-20200917204031802](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917204031802.png)

```properties
#bootstrap.properties
# 指定命名空间
spring.cloud.nacos.config.namespace=3d640f27-9ea9-41be-93b2-d394181f35f6 
# 指定分组
spring.cloud.nacos.config.group=prod
```



## 加载多个配置集

>  上面的已经做到了 : 为每个微服务在不同的环境下指定不同的配置文件。但是只有一个配置文件，实际开发中每个微服务需要用到很多个配置文件。即我们要做到<font color=ff00aa>为每个微服务在不同的环境下指定多个不同的配置文件</font>

1. 新增数据源配置文件

	![image-20200917205117965](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917205117965.png)

2. 新增多个配置集

	![image-20200917205621694](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917205621694.png)

3. 添加配置

	```properties
	#bootstrap.properties
	# 加载多个结果集
	spring.cloud.nacos.config.ext-config[0].data-id=datasource.yml
	spring.cloud.nacos.config.ext-config[0].group=Dev
	# 是否动态加载
	spring.cloud.nacos.config.ext-config[0].refresh=true
	
	spring.cloud.nacos.config.ext-config[1].data-id=mybatis.yml
	spring.cloud.nacos.config.ext-config[1].group=Dev
	# 是否动态加载
	spring.cloud.nacos.config.ext-config[1].refresh=true
	
	
	spring.cloud.nacos.config.ext-config[2].data-id=other.yml
	spring.cloud.nacos.config.ext-config[2].group=Dev
	# 是否动态加载
	spring.cloud.nacos.config.ext-config[2].refresh=true
	```



4. 项目启动时可以看到

	![image-20200917210721769](第三章-Nacos配置中心(SpringCloud Config).assets/image-20200917210721769.png)

> 配置中心中没有则读取配置文件



官网 ：https://nacos.io/zh-cn/docs/what-is-nacos.html