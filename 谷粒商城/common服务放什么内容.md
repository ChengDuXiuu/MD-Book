`jar包`

* Mybatis相关

	```xml
	<!-- 数据库 -->
	<dependency>
	  <groupId>com.baomidou</groupId>
	  <artifactId>mybatis-plus-boot-starter</artifactId>
	  <version>${mybatis.version}</version>
	</dependency>
	<dependency>
	  <groupId>mysql</groupId>
	  <artifactId>mysql-connector-java</artifactId>
	  <version>${mysql.version}</version>
	</dependency>
	```

	

* 网络

	```xml
	<!-- 网络  -->
	<!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpcore -->
	<dependency>
	  <groupId>org.apache.httpcomponents</groupId>
	  <artifactId>httpcore</artifactId>
	  <version>${httpcomponents.vresion}</version>
	</dependency>
	<dependency>
	  <groupId>javax.servlet</groupId>
	  <artifactId>servlet-api</artifactId>
	  <version>${servlet.version}</version>
	  <scope>provided</scope>
	</dependency>
	<dependency>
	  <groupId>commons-lang</groupId>
	  <artifactId>commons-lang</artifactId>
	  <version>${commons.lang.version}</version>
	</dependency>
	```

* 工具类

	```xml
	<!-- 工具类 -->
	<dependency>
	  <groupId>org.projectlombok</groupId>
	  <artifactId>lombok</artifactId>
	  <version>${lombok.version}</version>
	</dependency>
	<!-- fastjson -->
	<dependency>
	  <groupId>com.alibaba</groupId>
	  <artifactId>fastjson</artifactId>
	  <version>${fastjson.version}</version>
	</dependency>
	<!-- pojo 类转换 -->
	<dependency>
	  <groupId>org.mapstruct</groupId>
	  <artifactId>mapstruct</artifactId>
	  <version>${mapstruct.version}</version>
	</dependency>
	```

* 参数校验

	```xml
	<dependency>
	  <groupId>jakarta.validation</groupId>
	  <artifactId>jakarta.validation-api</artifactId>
	</dependency>
	```

* 服务注册发现、配置中心

	```xml
	<!--服务注册发现-->
	<dependency>
	  <groupId>com.alibaba.cloud</groupId>
	  <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
	</dependency>
	
	<!--服务配置中心-->
	<dependency>
	  <groupId>com.alibaba.cloud</groupId>
	  <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
	</dependency>
	```

`公用`

* 通用枚举类
* 全局异常处理 状态码
* 分页工具类
* 全局异常类
* 全局统一返回类
* TO | VO 最好把所有DTO也放到这里
* 参数校验【自定义参数校验注解等】
* ~~pojo转换器接口【放在各自的服务代码中】~~
* 