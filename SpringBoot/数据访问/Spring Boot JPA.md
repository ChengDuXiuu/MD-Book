摘要: 原创出处 http://www.iocoder.cn/Spring-Boot/JPA/ 「芋道源码」欢迎转载，保留摘要，谢谢！

- [1. 概述](http://www.iocoder.cn/Spring-Boot/JPA/)
- [2. 快速入门](http://www.iocoder.cn/Spring-Boot/JPA/)
- [3. 分页操作](http://www.iocoder.cn/Spring-Boot/JPA/)
- [4. 基于方法名查询](http://www.iocoder.cn/Spring-Boot/JPA/)
- [5. 基于注解查询](http://www.iocoder.cn/Spring-Boot/JPA/)
- [666. 彩蛋](http://www.iocoder.cn/Spring-Boot/JPA/)

------

------

> 本文在提供完整代码示例，可见 https://github.com/YunaiV/SpringBoot-Labs 的 [lab-13-spring-data-jpa](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa) 目录。
>
> 原创不易，给点个 [Star](https://github.com/YunaiV/SpringBoot-Labs/stargazers) 嘿，一起冲鸭！

# 1. 概述

我们，咱们来学习下 [Spring Data JPA](https://spring.io/projects/spring-data-jpa) 。

相信不少胖友之前有了解过 JPA、Hibernate ，那么 JPA、Hibernate、Spring Data JPA 这三者是什么关系呢？我们来一起理一理。

[**JPA**](https://zh.wikipedia.org/wiki/Java持久化API) ，全称 Java Persistence API ，是由 Java 定义的 Java ORM 以及实体操作 API 的标准。正如最早学习 JDBC 规范，Java 自身并未提供相关的实现，而是 MySQL 提供 MySQL [mysql-connector-java](https://mvnrepository.com/artifact/mysql/mysql-connector-java) 驱动，Oracle 提供 [oracle-jdbc](https://mvnrepository.com/artifact/oracle/oracle-jdbc) 驱动。而实现 JPA 规范的有：

- [Hibernate ORM](https://hibernate.org/orm/)
- [Oracle TopLink](https://www.oracle.com/middleware/technologies/top-link.html)
- [Apache OpenJPA](http://openjpa.apache.org/)

Spring Data JPA ，是 [Spring Data](https://spring.io/projects/spring-data) 提供的一套**简化**的 JPA 开发的框架。

- 内置 CRUD、分页、排序等功能的操作。
- 根据约定好的[方法名](https://docs.spring.io/spring-data/jpa/docs/2.2.0.RELEASE/reference/html/#jpa.query-methods.query-creation)规则，自动生成对应的查询操作。
- 使用 `@Query` 注解，自定义 SQL 。

所以，绝大多数情况下，我们无需编写代码，直接调用 JPA 的 API 。也因此，在我们使用的 Spring Data JPA 的项目中，如果想要替换底层使用的 JPA 实现框架，在未使用到相关 JPA 实现框架的特殊特性的情况下，可以透明替换。

> 关于这一点，我们在 [《芋道 Spring Boot Redis 入门》](http://www.iocoder.cn/Spring-Boot/Redis/?self) 中，已经看到 [Spring Data Redis](https://spring.io/projects/spring-data-redis) 也是已经看到这样的好处。

总的来说，就是如下一张图：

> FROM [《spring data jpa hibernate jpa 三者之间的关系》](https://www.cnblogs.com/xiaoheike/p/5150553.html)
>
> ![img](Spring Boot JPA.assets/e55f8f7084a6d6e9e908de509c7f5a54.png)

当然，绝大多数情况下，我们使用的 JPA 实现框架是 [Hibernate ORM](https://hibernate.org/orm/) 。所以整个调用过程是：

```
应用程序 => Repository => Spring Data JPA => Hibernate
```

# 2. 快速入门

> 示例代码对应仓库：[lab-13-jpa](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa) 。

本小节，我们会使用 [`spring-boot-starter-data-jpa`](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-jpa) 自动化配置 Spring Data JPA 。同时，演示 Spring Data JPA 的 CRUD 的操作。

## 2.1 引入依赖

在 [`pom.xml`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/pom.xml) 文件中，引入相关依赖。

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>lab-13-jpa</artifactId>

    <dependencies>
        <!-- 实现对数据库连接池的自动化配置 -->
        <!-- 实际上 spring-boot-starter-data-jpa 已经包括 spring-boot-starter-jdbc -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency> <!-- 本示例，我们使用 MySQL -->
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.48</version>
        </dependency>

        <!-- 实现对 Spring Data JPA 的自动化配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- 方便等会写单元测试 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

具体每个依赖的作用，胖友自己认真看下艿艿添加的所有注释噢。

另外，在 `spring-boot-starter-data-jpa` 中，已经默认引入了 Hibernate 的依赖。

## 2.2 Application

创建 [`Application.java`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/Application.java) 类，配置 `@SpringBootApplication` 注解即可。代码如下：

```
// Application.java

@SpringBootApplication
public class Application {
}
```

## 2.3 配置文件

在 [`application.yml`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/resources/application.yaml) 中，添加 JPA 配置，如下：

```
spring:
  # datasource 数据源配置内容
  datasource:
    url: jdbc:mysql://47.112.193.81:3306/testb5f4?useSSL=false&useUnicode=true&characterEncoding=UTF-8
    driver-class-name: com.mysql.jdbc.Driver
    username: testb5f4
    password: F4df4db0ed86@11
  # JPA 配置内容，对应 JpaProperties 类
  jpa:
    show-sql: true # 打印 SQL 。生产环境，建议关闭
    # Hibernate 配置内容，对应 HibernateProperties 类
    hibernate:
      ddl-auto: none
```

- `datasource` 配置项，配置 datasource 数据源配置内容。

- `jpa` 配置项，配置 Spring Data JPA 配置内容，对应 [`org.springframework.boot.autoconfigure.orm.jpa.JpaProperties.java`](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/orm/jpa/JpaProperties.java) 类。

- `hibernate` 配置项，配置 Hibernate 配置内容，对应 [`org.springframework.boot.autoconfigure.orm.jpa.HibernateProperties.java`](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/orm/jpa/HibernateProperties.java) 类。

	- 实际项目无需配置 `hibernate` 配置项，这里仅仅是演示，让胖友知道这回事。

	- `ddl-auto` 配置项，设置 Hibernate DDL 处理策略。一共有 `none`、`create`、`create-drop`、`update`、`validate` 五个选项。

		> FROM [《jpa 的 hibernate.ddl-auto 的几个属性值区别》](https://blog.csdn.net/qq_36666651/article/details/80719259)
		>
		> - create ：每次加载 hibernate 时都会删除上一次的生成的表，然后根据你的 model 类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
		> - create-drop ：每次加载 hibernate 时根据 model 类生成表，但是 sessionFactory 一关闭，表就自动删除。
		> - update ：最常用的属性，第一次加载 hibernate 时根据 model 类会自动建立起表的结构（前提是先建立好数据库），以后加载 hibernate 时根据 model 类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
		> - validate ：每次加载 hibernate 时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。
		> 	- 建议，生产环境下，建议配置 `none` ，不使用 Hibernate Auto DDL 功能。😈 启动个项目，就自动变更数据库表结构，多危险啊~

## 2.4 UserDO

在 [`cn.iocoder.springboot.lab13.jpa.dataobject`](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/dataobject) 包路径下，创建 [UserDO.java](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/dataobject/UserDO.java) 类，用户 DO 。代码如下：

```
// UserDO.java

@Entity
@Table(name = "users")
public class UserDO {

    /**
     * 用户编号
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY,  // strategy 设置使用数据库主键自增策略；
            generator = "JDBC") // generator 设置插入完成后，查询最后生成的 ID 填充到该属性中。
    private Integer id;
    /**
     * 账号
     */
    @Column(nullable = false)
    private String username;
    /**
     * 密码（明文）
     *
     * ps：生产环境下，千万不要明文噢
     */
    @Column(nullable = false)
    private String password;
    /**
     * 创建时间
     */
    @Column(name = "create_time", nullable = false)
    private Date createTime;

    // ... 省略 setting/getting 方法

}
```

关于 JPA 的注解的详细说明，胖友后面再看看 [《Spring Data JPA 中常用的注解详解》](https://www.jianshu.com/p/1b759ef26ff3) 文章。我们，继续往下看。

对应的创建表的 SQL 如下：

```
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '用户编号',
  `username` varchar(64) COLLATE utf8mb4_bin DEFAULT NULL COMMENT '账号',
  `password` varchar(32) COLLATE utf8mb4_bin DEFAULT NULL COMMENT '密码',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_username` (`username`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;
```

## 2.5 UserRepository01

在 [`cn.iocoder.springboot.lab13.mybatis.repository`](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository) 包路径下，创建 [UserRepository01](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository01.java) 接口。代码如下：

```
// UserRepository01.java

public interface UserRepository01 extends CrudRepository<UserDO, Integer> {

}
```

- 继承 `org.springframework.data.repository.CrudRepository` 接口，第一个泛型设置对应的实体是 UserDO ，第二个泛型设置对应的主键类型是 Integer 。
- 因为实现了 CrudRepository 接口，Spring Data JPA 会自动生成对应的 CRUD 的代码。具体 CrudRepository 提供了哪些操作，胖友点击 [`CrudRepository.java`](https://github.com/spring-projects/spring-data-commons/blob/master/src/main/java/org/springframework/data/repository/CrudRepository.java) 查看。

## 2.6 简单测试

创建 [UserRepository01Test](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/test/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository01Test.java) 测试类，我们来测试一下简单的 UserRepository01 的每个操作。代码如下：

```
// UserRepository01.java

@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class UserMapperTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testInsert() {
        UserDO user = new UserDO().setUsername(UUID.randomUUID().toString())
                .setPassword("nicai").setCreateTime(new Date());
        userMapper.insert(user);
    }

    @Test
    public void testUpdateById() {
        UserDO updateUser = new UserDO().setId(1)
                .setPassword("wobucai");
        userMapper.updateById(updateUser);
    }

    @Test
    public void testDeleteById() {
        userMapper.deleteById(2);
    }

    @Test
    public void testSelectById() {
        userMapper.selectById(1);
    }

    @Test
    public void testSelectByUsername() {
        userMapper.selectByUsername("yunai");
    }

    @Test
    public void testSelectByIds() {
        List<UserDO> users = userMapper.selectByIds(Arrays.asList(1, 3));
        System.out.println("users：" + users.size());
    }

}
```

具体的，胖友可以自己跑跑，妥妥的。

# 3. 分页操作

> 示例代码对应仓库：[lab-13-jpa](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa) 。

Spring Data 提供 [`org.springframework.data.repository.PagingAndSortingRepository`](https://github.com/spring-projects/spring-data-commons/blob/master/src/main/java/org/springframework/data/repository/PagingAndSortingRepository.java) 接口，继承 CrudRepository 接口，在 CRUD 操作的基础上，额外提供分页和排序的操作。代码如下：

```
// PagingAndSortingRepository.java

public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {

	Iterable<T> findAll(Sort sort); // 排序操作

	Page<T> findAll(Pageable pageable); // 分页操作

}
```

## 3.1 UserRepository02

在 [`cn.iocoder.springboot.lab13.mybatis.repository`](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository) 包路径下，创建 [UserRepository02](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository02.java) 接口。代码如下：

```
// UserRepository02.java

public interface UserRepository02 extends PagingAndSortingRepository<UserDO, Integer> {

}
```

- 实现 PagingAndSortingRepository 接口，第一个泛型设置对应的实体是 UserDO ，第二个泛型设置对应的主键类型是 Integer 。

## 3.2 简单测试

创建 [UserRepository02Test](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/test/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository02Test.java) 测试类，我们来测试一下简单的 UserRepository02 的每个操作。代码如下：

```
// UserRepository02Test.java

@RunWith(SpringRunner.class)
@SpringBootTest
public class UserRepository02Test {

    @Autowired
    private UserRepository02 userRepository;

    @Test // 排序
    public void testFindAll() {
        // 创建排序条件
        Sort sort = new Sort(Sort.Direction.DESC, "id");
        // 执行排序操作
        Iterable<UserDO> iterable = userRepository.findAll(sort);
        // 打印
        iterable.forEach(System.out::println);
    }

    @Test // 分页
    public void testFindPage() {
        // 创建排序条件
        Sort sort = new Sort(Sort.Direction.DESC, "id");
        // 创建分页条件
        Pageable pageable = PageRequest.of(1, 10, sort);
        // 执行分页操作
        Page<UserDO> page = userRepository.findAll(pageable);
        // 打印
        System.out.println(page.getTotalElements());
        System.out.println(page.getTotalPages());
    }

}
```

具体的，胖友可以自己跑跑，妥妥的。

# 4. 基于方法名查询

> 示例代码对应仓库：[lab-13-jpa](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa) 。

在 Spring Data 中，支持根据方法名作生成对应的查询（`WHERE`）条件，进一步进化我们使用 JPA ，具体是方法名以 `findBy`、`existsBy`、`countBy`、`deleteBy` 开头，后面跟具体的条件。具体的规则，在 [《Spring Data JPA —— Query Creation》](https://docs.spring.io/spring-data/jpa/docs/2.2.0.RELEASE/reference/html/#jpa.query-methods.query-creation) 文档中，已经详细提供。如下：

| 关键字                 | 方法示例                                                     | JPQL snippet                                                 |
| :--------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `And`                  | `findByLastnameAndFirstname`                                 | `… where x.lastname = ?1 and x.firstname = ?2`               |
| `Or`                   | `findByLastnameOrFirstname`                                  | `… where x.lastname = ?1 or x.firstname = ?2`                |
| `Is`, `Equals`         | `findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1`                                   |
| `Between`              | `findByStartDateBetween`                                     | `… where x.startDate between ?1 and ?2`                      |
| `LessThan`             | `findByAgeLessThan`                                          | `… where x.age < ?1`                                         |
| `LessThanEqual`        | `findByAgeLessThanEqual`                                     | `… where x.age <= ?1`                                        |
| `GreaterThan`          | `findByAgeGreaterThan`                                       | `… where x.age > ?1`                                         |
| `GreaterThanEqual`     | `findByAgeGreaterThanEqual`                                  | `… where x.age >= ?1`                                        |
| `After`                | `findByStartDateAfter`                                       | `… where x.startDate > ?1`                                   |
| `Before`               | `findByStartDateBefore`                                      | `… where x.startDate < ?1`                                   |
| `IsNull`, `Null`       | `findByAge(Is)Null`                                          | `… where x.age is null`                                      |
| `IsNotNull`, `NotNull` | `findByAge(Is)NotNull`                                       | `… where x.age not null`                                     |
| `Like`                 | `findByFirstnameLike`                                        | `… where x.firstname like ?1`                                |
| `NotLike`              | `findByFirstnameNotLike`                                     | `… where x.firstname not like ?1`                            |
| `StartingWith`         | `findByFirstnameStartingWith`                                | `… where x.firstname like ?1` (parameter bound with appended `%`) |
| `EndingWith`           | `findByFirstnameEndingWith`                                  | `… where x.firstname like ?1` (parameter bound with prepended `%`) |
| `Containing`           | `findByFirstnameContaining`                                  | `… where x.firstname like ?1` (parameter bound wrapped in `%`) |
| `OrderBy`              | `findByAgeOrderByLastnameDesc`                               | `… where x.age = ?1 order by x.lastname desc`                |
| `Not`                  | `findByLastnameNot`                                          | `… where x.lastname <> ?1`                                   |
| `In`                   | `findByAgeIn(Collection ages)`                               | `… where x.age in ?1`                                        |
| `NotIn`                | `findByAgeNotIn(Collection ages)`                            | `… where x.age not in ?1`                                    |
| `True`                 | `findByActiveTrue()`                                         | `… where x.active = true`                                    |
| `False`                | `findByActiveFalse()`                                        | `… where x.active = false`                                   |
| `IgnoreCase`           | `findByFirstnameIgnoreCase`                                  | `… where UPPER(x.firstame) = UPPER(?1)`                      |

- 注意，如果我们有排序需求，可以使用 `OrderBy` 关键字。

下面，我们来编写一个简单的示例。

> 艿艿：IDEA 牛逼，提供的插件已经能够自动提示上述关键字。太强了~

## 4.1 UserRepository03

在 [`cn.iocoder.springboot.lab13.mybatis.repository`](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository) 包路径下，创建 [UserRepository03](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository03.java) 接口。代码如下：

```
// UserRepository03.java

public interface UserRepository03 extends PagingAndSortingRepository<UserDO, Integer> {

    UserDO findByUsername(String username);

    Page<UserDO> findByCreateTimeAfter(Date createTime, Pageable pageable);

}
```

- 对于分页操作，需要使用到 Pageable 参数，需要作为方法的最后一个参数。

## 4.2 简单测试

创建 [UserRepository03Test](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/test/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository03Test.java) 测试类，我们来测试一下简单的 UserRepository03 的每个操作。代码如下：

```
// UserRepository03.java

@RunWith(SpringRunner.class)
@SpringBootTest
public class UserRepository03Test {

    @Autowired
    private UserRepository03 userRepository;

    @Test
    public void testFindByUsername() {
        UserDO user = userRepository.findByUsername("yunai");
        System.out.println(user);
    }

    @Test
    public void testFindByCreateTimeAfter() {
        // 创建分页条件
        Pageable pageable = PageRequest.of(1, 10);
        // 执行分页操作
        Date createTime = new Date(2018 - 1990, Calendar.FEBRUARY, 24); // 临时 Demo ，实际不建议这么写
        Page<UserDO> page = userRepository.findByCreateTimeAfter(createTime, pageable);
        // 打印
        System.out.println(page.getTotalElements());
        System.out.println(page.getTotalPages());
    }

}
```

具体的，胖友可以自己跑跑，妥妥的。

# 5. 基于注解查询

虽然 Spring Data JPA 提供了非常强大的功能，可以满足绝大多数业务场景下的 CRUD 操作，但是可能部分情况下，我们可以使用在方法上添加 [`org.springframework.data.jpa.repository.@Query`](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/Query.html) 注解，实现自定义的 SQL 操作。

如果是更新或删除的 SQL 操作，需要**额外**在方法上添加 [`org.springframework.data.jpa.repository.@Modifying`](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/Modifying.html) 注解。

下面，我们来编写一个简单的示例。

## 5.1 UserRepository04

在 [`cn.iocoder.springboot.lab13.mybatis.repository`](https://github.com/YunaiV/SpringBoot-Labs/tree/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository) 包路径下，创建 [UserRepository04](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/main/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository04.java) 接口。代码如下：

```
// UserRepository04.java

public interface UserRepository04 extends PagingAndSortingRepository<UserDO, Integer> {

    @Query("SELECT u FROM UserDO u WHERE u.username = ?1")
    UserDO findByUsername01(String username); // <1>

    @Query("SELECT u FROM UserDO u WHERE u.username = :username")
    UserDO findByUsername02(@Param("username") String username); // <2>

    @Query(value = "SELECT * FROM users u WHERE u.username = :username", nativeQuery = true)
    UserDO findByUsername03(@Param("username") String username); // <3>

    @Query("UPDATE UserDO  u SET u.username = :username WHERE u.id = :id")
    @Modifying
    int updateUsernameById(Integer id, String username); // <4>

}
```

- `<1>` 处，使用 `@Query` 自定义了一个 SQL 操作，并且参数使用**占位符(`?`)** + **参数位置**的形式。

- `<2>` 处，和 `<1>` 类似，差异在于使用**占位符(`:`)** + **参数名字(需要使用 `@Param` 声明)**的形式。

- ```
	<3>
	```

	 

	处，和

	 

	```
	<2>
	```

	 

	类似，差别在于我们增加了

	 

	```
	nativeQuery = true
	```

	 

	，表示在

	 

	```
	@Query
	```

	 

	自定义的是

	原生 SQL

	，而非在

	 

	```
	<1>
	```

	 

	和

	 

	```
	<2>
	```

	 

	自定义的是

	 

	JPQL

	 

	。进一步的说：

	- `<1>` 和 `<2>` 处，`FROM UserDO` ，使用的是实体名。
	- `<3>` 处，使用的是表名。
	- 对 JPQL 不是很了解的胖友，可以看看 [《JPQL 的学习》](https://www.jianshu.com/p/4a4410075bab) 文章。

- `<4>` 处，定义了更新操作，需要加上 `@Modifying` 注解。😈 另外，我们发可以现，使用参数名时，可以不用配合 `@Param` 注解。

## 5.2 简单测试

创建 [UserRepository04Test](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-13-spring-data-jpa/lab-13-jpa/src/test/java/cn/iocoder/springboot/lab13/jpa/repository/UserRepository04Test.java) 测试类，我们来测试一下简单的 UserRepository04 的每个操作。代码如下：

```
// UserRepository04Test.java

@RunWith(SpringRunner.class)
@SpringBootTest
public class UserRepository04Test {

    @Autowired
    private UserRepository04 userRepository;

    @Test
    public void testFindIdByUsername01() {
        UserDO user = userRepository.findByUsername01("yunai");
        System.out.println(user);
    }

    @Test
    public void testFindIdByUsername02() {
        UserDO user = userRepository.findByUsername02("yunai");
        System.out.println(user);
    }

    @Test
    public void testFindIdByUsername03() {
        UserDO user = userRepository.findByUsername03("yunai");
        System.out.println(user);
    }

    @Test
    // 更新操作，需要在事务中。
    // 在单元测试中，事务默认回滚，所以胖友可能怎么测试，事务都不更新。
    @Transactional
    public void testUpdateUsernameById() {
        userRepository.updateUsernameById(5, "yudaoyuanma");
    }

}
```

具体的，胖友可以自己跑跑，妥妥的。

对于分页操作，需要在 `@Query` 编写查询分页列表和记录总数两条 SQL 。示例如下：

```
public interface UserRepository extends JpaRepository<User, Long> {

  @Query(value = "SELECT * FROM USERS WHERE LASTNAME = ?1", // value 属性，编写查询分页列表的 SQL 。
    countQuery = "SELECT count(*) FROM USERS WHERE LASTNAME = ?1", // countQuery 属性，编写记录总数的 SQL 。
    nativeQuery = true)
  Page<User> findByLastname(String lastname, Pageable pageable);

}
```

# 666. 彩蛋

😈 本文仅仅是 Spring Data JPA 的简单入门，还有部分内容，胖友可以自己在去学习下：

- [《Using JPA Named Queries》](https://docs.spring.io/spring-data/jpa/docs/2.2.0.RELEASE/reference/html/#jpa.query-methods.named-queries) ，可以使用 XML 自定义 SQL 操作。

- [《Spring Data JPA 实现逻辑删除》](https://my.oschina.net/weechang93/blog/1576594) ，绝大多数业务场景下，我们不会使用 `DELETE` 物理删除，而是通过标志位进行逻辑删除。

- 多表查询

	- 方式一：[《JPA 多表查询的解决办法》](https://blog.wuwii.com/jpa-query-muti.html)
	- 方式二：[《JPA 多表关联查询》](https://blog.csdn.net/moshowgame/article/details/80058270)

- [《Spring Data JPA 使用 Example 快速实现动态查询》](https://blog.csdn.net/long476964/article/details/79677526)

	> 艿艿，如果在这种情况下，Repository 需要继承 JpaRepository 接口。

如果胖友想找一个完整的，使用 JPA 的项目，可以看看 [Apollo](https://github.com/ctripcorp/apollo) 。它是携程开源的配置中心，目前最好用的配置中心，基本没有之一，嘿嘿。