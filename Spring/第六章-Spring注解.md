## @Value

`含义:`

​	属性值注入，有setter方法也可以加到setter方法上，适用于属性为基本类型。

### 1、属性代码中赋值

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