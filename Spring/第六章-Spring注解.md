## @Value

`含义:`

​	属性值注入，有setter方法也可以加到setter方法上，适用于属性为基本类型。

### 1、属性代码中赋值

`普通值-Spring`

```java
@Slf4j
@RunWith(SpringRunner.class)
public class Client {
    @Value("1000")
    private int value;

    @Test
    public void testValue(){
        log.error("value 值 ：{}",value);
    }
}
```

`随机值-SpringBoot`

```java
@SpringBootTest
@Slf4j
class SpringBootTestApplicationTests {
    @Value("${random.int(10)}")
    private int random;
    
    /*
         * @Author No1.shuai
         * @Description //TODO 静态读取，即项目启动后配置文件改变不会影响值，单例，项目启动后每次调用数据值都是一样
         * @Date 15:53 15:53
         **/
        @Test
        public void randomTest(){
            for (int i = 0; i < 10; i++) {
                log.error(random+"====");
            }
        }
}
```



### 2、属性配置文件中读取值

`Spring`

https://blog.csdn.net/weixin_43942304/article/details/108920730

`SpringBoot`

```properties
# 应用名称
spring.application.name=SpringBoot-Test
# 应用服务 WEB 访问端口
server.port=8080
```

```java
@SpringBootTest
@Slf4j
class SpringBootTestApplicationTests {

    @Value("${spring.application.name}")
    private String name;
    @Test
    void contextLoads() {
        log.error(name);
    }
}
```



>   <font color=ff00aa size=5>官方并不建议 使用  @Value进行绑定，推荐如下：</font>



## @ConfigurationProperties

参考：https://gitee.com/SnailClimb/springboot-guide/blob/master/docs/basis/read-config-properties.md



### 1、简单读取

1.  配置文件

    ```yaml
    library:
      location: 湖北武汉加油中国加油
      books:
        - name: 天才基本法
          description: 二十二岁的林朝夕在父亲确诊阿尔茨海默病这天，得知自己暗恋多年的校园男神裴之即将出国深造的消息——对方考取的学校，恰是父亲当年为她放弃的那所。
        - name: 时间的秩序
          description: 为什么我们记得过去，而非未来？时间“流逝”意味着什么？是我们存在于时间之内，还是时间存在于我们之中？卡洛·罗韦利用诗意的文字，邀请我们思考这一亘古难题——时间的本质。
        - name: 了不起的我
          description: 如何养成一个新习惯？如何让心智变得更成熟？如何拥有高质量的关系？ 如何走出人生的艰难时刻？
    ```

2.  编写实体类与配置文件对应

    ```java
    @Component
    @ConfigurationProperties(prefix = "library")//对应yml文件中 library: 旗下key对应实体类属性
    @Data
    @ToString
    public class LibraryProperties {
        private String location;
        private List<Book> books;
    
        @Data
        @ToString
        static class Book {
            String name;
            String description;
        }
    }
    ```

3.  启动类添加扫描注解

    ```java
    @SpringBootApplication
    @ComponentScan({"com.shuai.completableFuture","com.shuai.configurationProperties"})
    public class SpringBootTestApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(SpringBootTestApplication.class, args);
        }
    }
    ```

4.  测试

    ```java
    @SpringBootTest
    @Slf4j
    public class ConfigurationPropertiesTest {
    
        @Autowired
        private LibraryProperties libraryProperties;
    
        @Test
        public void test(){
            log.error(libraryProperties.toString());
        }
    }
    //2021-05-18 16:27:12.354 ERROR 7144 --- [           main] c.s.test.ConfigurationPropertiesTest     : LibraryProperties(location=湖北武汉加油中国加油, books=[LibraryProperties.Book(name=天才基本法, description=二十二岁的林朝夕在父亲确诊阿尔茨海默病这天，得知自己暗恋多年的校园男神裴之即将出国深造的消息——对方考取的学校，恰是父亲当年为她放弃的那所。), LibraryProperties.Book(name=时间的秩序, description=为什么我们记得过去，而非未来？时间“流逝”意味着什么？是我们存在于时间之内，还是时间存在于我们之中？卡洛·罗韦利用诗意的文字，邀请我们思考这一亘古难题——时间的本质。), LibraryProperties.Book(name=了不起的我, description=如何养成一个新习惯？如何让心智变得更成熟？如何拥有高质量的关系？ 如何走出人生的艰难时刻？)])
    ```



### 2、通过@ConfigurationProperties读取并校验

1.  yaml文件添加如下内容

    ```yaml
    my-profile:
      name: Guide哥
      email: koushuangbwcx@
    ```

2.  修改实体类

    ```java
    @Component
    @ConfigurationProperties(prefix = "my-profile")//对应yml文件中 library: 旗下key对应实体类属性
    @Data
    @ToString
    @Validated
    public class LibraryProperties {
        @NotEmpty
        private String name;
    
        @Email
        @NotEmpty
        private String email;
    
        //配置文件中没有读取到的话就用默认值
        private Boolean handsome = Boolean.TRUE;
    }
    ```

3.  测试

    ```bash
    Property: my-profile.email
    Value: koushuangbwcx@
    Origin: class path resource [application.yml]:14:10
    Reason: 不是一个合法的电子邮件地址
    ```



### 3、@PropertySource读取指定 properties 文件

1.  配置文件

    ```properties
    # 应用名称
    spring.application.name=SpringBoot-Test
    # 应用服务 WEB 访问端口
    server.port=8080
    
    thread.pool.time=5000
    ```

2.  实体类对应配置文件

    ```java
    @Component
    @PropertySource("classpath:application.properties")
    @ConfigurationProperties(prefix = "thread.pool")
    @Data
    @Validated
    public class AppointPro {
    //    @Value("${thread.pool.time}")
        @NotEmpty
        private String time;
    }
    ```

3.  测试

    ```java
    @SpringBootTest
    @Slf4j
    public class AppointProTest {
        @Autowired
        private AppointPro appointPro;
        @Test
        public void test(){
            log.error(appointPro.getTime());
        }
    }
    //2021-05-18 16:48:51.972 ERROR 17372 --- [           main] com.shuai.test.AppointProTest            : 5000
    ```

    

