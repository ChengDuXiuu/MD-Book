## IOC能干什么

* 松耦合
	* 以前我们都是在类中实例其他依赖类从而获取其对象进行调用。有了IOC容器帮我们管理对象后，我们将和业务代码不相干的通用的服务类或者工具类交由IOC容器管理。实现`依赖倒置原则`和`松耦合`.

* 管理Bean
	* 将对象、文件以及项目的其他资源交由`IOC容器`管理，在你需要的时候想`IOC容器索要即可`



<font color=ff00aa>提出问题 ？</font>

1. 既然要管理Bean (对象)，IOC容器怎么进行数据初始化、实例化？
2. 我们程序中又该如何使用IOC容器中的Bean呢？
3. 对象之间的依赖关系如何处理？



<font color=ff00aa>回答问题 ：</font>

1. 数据初始化的操作需要用到`元数据`。下面会讲到。
2. 使用Spring 提供的两大`IOC容器接口`。下面会讲到。
3. 使用Spring提供的`依赖注入`。下面会讲到。

## 元数据

**元数据（Metadata）**又称中介数据、中继数据，为描述数据的数据（data about data），主要是描述数据属性（property）的信息。

`元数据`可以是：

1. 配置文件 xml 、properties、yaml
2. Java配置文件
3. 注解
4. 自动装配



## IOC容器接口

可以帮助我们更好地操控IOC容器。

1. `BeanFactory`：BeanFactory 提供了 Spring 容器的配置框架和基本功能。

	* `ClassPathResource`：指定类路径下的资源文件

	* `FileSystemResource`：指定项目根路径或本地磁盘路径下的资源文件。

2. `ApplicationContext`：BeanFactory 的子接口。它还扩展了其他一些接口，以支持更丰富的功能，如：国际化、访问资源、事件机制、更方便的支持 AOP、在 web 应用中指定应用层上下文等。

	* `ClassPathXmlApplicationContext`: 此类加载的是src下的spring配置文件

		```java
		BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath.xml");
		```

	* `FileSystemXmlApplicationContext`: 此类加载的是项目根目录即和src同级目录下的spring配置文件

		或者是本机磁盘上的spring配置文件

		```java
		BeanFactory beanFactory = new FileSystemXmlApplicationContext("fileSystemConfig.xml");
		```

	* `AnnotationConfigApplicationContext`:实现基于Java的配置类加载Spring的应用上下文.避免使用application.xml进行配置

	* `XmlWebApplicationContext`:在Web应用中使用Spring容器

> * ApplicationContext容器会在初始化容器时将bean所有对象一次性装配好，执行效率高，占内存
>
> * BeanFactory采用延时加载策略，即在getBean执行时才会初始化需要的对象
>
> > 实际开发中，更推荐使用 `ApplicationContext` 作为 IoC 容器，因为它的功能远多于 `FactoryBean`



## IOC容器工作流程

通过上面的学习我们大概了解到：

1. Spring IOC容器可以帮我们管理对象，我们需要时获取即可。
2. 我们可以通过`元数据`定义Bean 之间的依赖关系 使得A调用C不会出现空指针的情况。
3. 我们也可以通过`容器接口`来获取Bean，很好的解决了 “数据从哪来，又到哪去 ”

下面我们来继续探索 IOC又是如何工作的呢

1. 配置元数据

	> 需要配置一些元数据来告诉 Spring，你希望容器如何工作，具体来说，就是如何去初始化、配置、管理 JavaBean 对象。而Spring是通过Resource接口来获取到用户自定义的配置文件或者注解

2. 实例化容器

	> 定位资源文件并依据元数据实例化并装配Bean  把用户定义好的Bean表示成IoC容器内部的数据结构，而这个容器内部的数据结构就是BeanDefinition。IoC 容器根据 BeanDefinition 进行实例化、配置及组装 Bean。

3. 使用Bean



接下来，我们通过案例来对 ==四种元数据 方式==进行分别探讨



### 配置文件方式

0. 准备代码

	```java
	public class UserDao {
	    public List<User> getAllUsers(){
	        List list=new ArrayList<String>();
	        list.add("a");
	        list.add("b");
	        list.add("c");
	        return list;
	    }
	}
	```

	```java
	public class UserService{
	
	    private UserDao  userDao;
	
	    // 构造器注入
	    public UserService(UserDao userDao) {
	        this.userDao = userDao;
	    }
	
	    public List<User> userList(){
	        return userDao.getAllUsers();
	    }
	}
	```

1. 配置元数据--XML

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <!--  UserService中依赖UserDao  因此IOC容器需要将UserDao注入到UserService   我们通过xml来进行配置来告诉IOC容器-->
	    <bean id="userService" class="com.shuai.springioc.service.UserService">
	        <constructor-arg ref="userDao"/>
	    </bean>
	    <bean id="userDao" class="com.shuai.springioc.dao.UserDao"/>
			 <import resource="resource2.xml" />
	</beans>
	```

	标签说明：

	- `<beans>` 是 Spring 配置文件的根节点。
	- `<bean>` 用来定义一个 JavaBean。`id` 属性是它的标识，在文件中必须唯一；`class` 属性是它关联的类。
	- `<alias>` 用来定义 Bean 的别名。
	- `<import>` 用来导入其他配置文件的 Bean 定义。这是为了加载多个配置文件，当然也可以把这些配置文件构造为一个数组（new String[] {“config1.xml”, config2.xml}）传给 `ApplicationContext` 实现类进行加载多个配置文件，那一个更适合由用户决定；这两种方式都是通过调用 Bean Definition Reader 读取 Bean 定义，内部实现没有任何区别。`<import>` 标签可以放在 `<beans>` 下的任何位置，没有顺序关系。

	> 组合 xml 配置文件 配置的 Bean 功能各不相同，都放在一个 xml 文件中，不便管理。 Java 设计模式讲究职责单一原则。配置其实也是如此，功能不同的 JavaBean 应该被组织在不同的 xml 文件中。然后使用 import 标签把它们统一导入。

	```XML
	<import resource="classpath:spring/applicationContext.xml"/>
	<import resource="/WEB-INF/spring/service.xml"/>
	```

2. 实例化容器

	```java
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("xml/xmlConfig.xml");
	```

3. 使用Bean

	```java
	UserService userService = (UserService) applicationContext.getBean("userService");
	System.out.println(userService.userList());
	```



> <font color=ff00aa>注意：</font>上面例子中存在：对象依赖。因此需要用到 `依赖注入-构造器注入`， 将UserDao注入到UserService  其中构造器注入只是注入的一中方式，下面会一一讲解。

### Java配置文件方式

0. 准备代码

	```java
	public class UserDao {
	    public List<User> getAllUsers(){
	        List list=new ArrayList<String>();
	        list.add("a");
	        list.add("b");
	        list.add("c");
	        return list;
	    }
	}
	```

	```java
	public class UserService{
	
	    private UserDao  userDao;
	
	    @Autowired
	    public UserService(UserDao userDao) {
	        this.userDao = userDao;
	    }
	
	    public List<User> userList(){
	        return userDao.getAllUsers();
	    }
	}
	```

1. 配置数据源--JAVA配置类

	```java
	@Configuration  // 声明这是一个配置类，程序运行时初始化这个类，把 @Bean 注解的 bean 加载到 ioc 容器备用
	public class UserConfig {
	    @Bean // 类似于<bean>  加载到 ioc 容器备用
	    public UserDao studentDao() {
	        return new UserDao();
	    }
	
	    @Bean // 类似于<bean>  加载到 ioc 容器备用  并将依赖类 UserDao传入
	    public UserService userService(UserDao userDao) {
	        return new UserService(userDao);
	    }
	}
	```

2. 实例化容器

	```java
	AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(UserConfig.class);
	```

	

3. 使用Bean

	```java
	UserService userService = (UserService) applicationContext.getBean("userService");
	        System.out.println(userService.userList());// 注意是 配置类中的方法名
	```



### 注解方式

#### 1、启动注解

Spring 默认是不启用注解的。如果想使用注解，需要先在 xml 中启动注解。 启动方式：在 xml 中加入一个标签，很简单吧。

```xml
<context:annotation-config/>
```

> 注：`<context:annotation-config/>` 只会检索定义它的上下文。什么意思呢？就是说，如果你 为 DispatcherServlet 指定了一个`WebApplicationContext`，那么它只在 controller 中查找`@Autowired`注解，而不会检查其它的路径。

#### 2、Spring 注解

- **`@Required`**

`@Required` 注解只能用于修饰 bean 属性的 setter 方法。受影响的 bean 属性必须在配置时被填充在 xml 配置文件中，否则容器将抛出`BeanInitializationException`。

```java
public class AnnotationRequired {
    private String name;
    private String sex;

    public String getName() {
        return name;
    }

    /**
     * @Required 注解用于bean属性的setter方法并且它指示，受影响的bean属性必须在配置时被填充在xml配置文件中，
     *           否则容器将抛出BeanInitializationException。
     */
    @Required
    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```

- **`@Autowired`**

`@Autowired`注解可用于修饰属性、setter 方法、构造方法。

> 注：`@Autowired`注解也可用于修饰构造方法，但如果类中只有默认构造方法，则没有必要。如果有多个构造器，至少应该修饰一个，来告诉容器哪一个必须使用。

可以使用 JSR330 的注解`@Inject`来替代`@Autowired`。

***范例\***

```java
public class AnnotationAutowired {
    private static final Logger log = LoggerFactory.getLogger(AnnotationRequired.class);

    @Autowired
    private Apple fieldA;

    private Banana fieldB;

    private Orange fieldC;

    public Apple getFieldA() {
        return fieldA;
    }

    public void setFieldA(Apple fieldA) {
        this.fieldA = fieldA;
    }

    public Banana getFieldB() {
        return fieldB;
    }

    @Autowired
    public void setFieldB(Banana fieldB) {
        this.fieldB = fieldB;
    }

    public Orange getFieldC() {
        return fieldC;
    }

    public void setFieldC(Orange fieldC) {
        this.fieldC = fieldC;
    }

    public AnnotationAutowired() {}

    @Autowired
    public AnnotationAutowired(Orange fieldC) {
        this.fieldC = fieldC;
    }

    public static void main(String[] args) throws Exception {
        AbstractApplicationContext ctx =
                        new ClassPathXmlApplicationContext("spring/spring-annotation.xml");

        AnnotationAutowired annotationAutowired =
                        (AnnotationAutowired) ctx.getBean("annotationAutowired");
        log.debug("fieldA: {}, fieldB:{}, fieldC:{}", annotationAutowired.getFieldA().getName(),
                        annotationAutowired.getFieldB().getName(),
                        annotationAutowired.getFieldC().getName());
        ctx.close();
    }
}
```

xml 中的配置

```xml
<!-- 测试@Autowired -->
<bean id="apple" class="org.zp.notes.spring.beans.annotation.sample.Apple"/>
<bean id="potato" class="org.zp.notes.spring.beans.annotation.sample.Banana"/>
<bean id="tomato" class="org.zp.notes.spring.beans.annotation.sample.Orange"/>
<bean id="annotationAutowired" class="org.zp.notes.spring.beans.annotation.sample.AnnotationAutowired"/>
```

- **`@Qualifier`**

在`@Autowired`注解中，提到了如果发现有多个候选的 bean 都符合修饰类型，Spring 就会抓瞎了。

那么，如何解决这个问题。

可以通过`@Qualifier`指定 bean 名称来锁定真正需要的那个 bean。

***范例\***

```java
public class AnnotationQualifier {
    private static final Logger log = LoggerFactory.getLogger(AnnotationQualifier.class);

    @Autowired
    @Qualifier("dog") /** 去除这行，会报异常 */
    Animal dog;

    Animal cat;

    public Animal getDog() {
        return dog;
    }

    public void setDog(Animal dog) {
        this.dog = dog;
    }

    public Animal getCat() {
        return cat;
    }

    @Autowired
    public void setCat(@Qualifier("cat") Animal cat) {
        this.cat = cat;
    }

    public static void main(String[] args) throws Exception {
        AbstractApplicationContext ctx =
                new ClassPathXmlApplicationContext("spring/spring-annotation.xml");

        AnnotationQualifier annotationQualifier =
                (AnnotationQualifier) ctx.getBean("annotationQualifier");

        log.debug("Dog name: {}", annotationQualifier.getDog().getName());
        log.debug("Cat name: {}", annotationQualifier.getCat().getName());
        ctx.close();
    }
}

abstract class Animal {
    public String getName() {
        return null;
    }
}

class Dog extends Animal {
    public String getName() {
        return "狗";
    }
}

class Cat extends Animal {
    public String getName() {
        return "猫";
    }
}
```

xml 中的配置

```xml
<!-- 测试@Qualifier -->
<bean id="dog" class="org.zp.notes.spring.beans.annotation.sample.Dog"/>
<bean id="cat" class="org.zp.notes.spring.beans.annotation.sample.Cat"/>
<bean id="annotationQualifier" class="org.zp.notes.spring.beans.annotation.sample.AnnotationQualifier"/>
```

#### 3、JSR 250 注解

@Resource

Spring 支持 JSP250 规定的注解`@Resource`。这个注解根据指定的名称来注入 bean。

如果没有为`@Resource`指定名称，它会像`@Autowired`一样按照类型去寻找匹配。

在 Spring 中，由`CommonAnnotationBeanPostProcessor`来处理`@Resource`注解。

***范例\***

```java
public class AnnotationResource {
    private static final Logger log = LoggerFactory.getLogger(AnnotationResource.class);

    @Resource(name = "flower")
    Plant flower;

    @Resource(name = "tree")
    Plant tree;

    public Plant getFlower() {
        return flower;
    }

    public void setFlower(Plant flower) {
        this.flower = flower;
    }

    public Plant getTree() {
        return tree;
    }

    public void setTree(Plant tree) {
        this.tree = tree;
    }

    public static void main(String[] args) throws Exception {
        AbstractApplicationContext ctx =
                        new ClassPathXmlApplicationContext("spring/spring-annotation.xml");

        AnnotationResource annotationResource =
                        (AnnotationResource) ctx.getBean("annotationResource");
        log.debug("type: {}, name: {}", annotationResource.getFlower().getClass(), annotationResource.getFlower().getName());
        log.debug("type: {}, name: {}", annotationResource.getTree().getClass(), annotationResource.getTree().getName());
        ctx.close();
    }
}
```

xml 的配置

```xml
<!-- 测试@Resource -->
<bean id="flower" class="org.zp.notes.spring.beans.annotation.sample.Flower"/>
<bean id="tree" class="org.zp.notes.spring.beans.annotation.sample.Tree"/>
<bean id="annotationResource" class="org.zp.notes.spring.beans.annotation.sample.AnnotationResource"/>
```

- **`@PostConstruct` 和 `@PreDestroy`**

`@PostConstruct` 和 `@PreDestroy` 是 JSR 250 规定的用于生命周期的注解。

从其名号就可以看出，一个是在构造之后调用的方法，一个是销毁之前调用的方法。

```java
public class AnnotationPostConstructAndPreDestroy {
    private static final Logger log = LoggerFactory.getLogger(AnnotationPostConstructAndPreDestroy.class);

    @PostConstruct
    public void init() {
        log.debug("call @PostConstruct method");
    }

    @PreDestroy
    public void destroy() {
        log.debug("call @PreDestroy method");
    }
}
```

#### 4、JSR 330 注解

从 Spring3.0 开始，Spring 支持 JSR 330 标准注解（依赖注入）。

注：如果要使用 JSR 330 注解，需要使用外部 jar 包。

若你使用 maven 管理 jar 包，只需要添加依赖到 pom.xml 即可：

```xml
<dependency>
  <groupId>javax.inject</groupId>
  <artifactId>javax.inject</artifactId>
  <version>1</version>
</dependency>
```

@Inject

`@Inject`和`@Autowired`一样，可以修饰属性、setter 方法、构造方法。

***范例\***

```java
public class AnnotationInject {
    private static final Logger log = LoggerFactory.getLogger(AnnotationInject.class);
    @Inject
    Apple fieldA;

    Banana fieldB;

    Orange fieldC;

    public Apple getFieldA() {
        return fieldA;
    }

    public void setFieldA(Apple fieldA) {
        this.fieldA = fieldA;
    }

    public Banana getFieldB() {
        return fieldB;
    }

    @Inject
    public void setFieldB(Banana fieldB) {
        this.fieldB = fieldB;
    }

    public Orange getFieldC() {
        return fieldC;
    }

    public AnnotationInject() {}

    @Inject
    public AnnotationInject(Orange fieldC) {
        this.fieldC = fieldC;
    }

    public static void main(String[] args) throws Exception {
        AbstractApplicationContext ctx =
                        new ClassPathXmlApplicationContext("spring/spring-annotation.xml");
        AnnotationInject annotationInject = (AnnotationInject) ctx.getBean("annotationInject");

        log.debug("type: {}, name: {}", annotationInject.getFieldA().getClass(),
                        annotationInject.getFieldA().getName());

        log.debug("type: {}, name: {}", annotationInject.getFieldB().getClass(),
                        annotationInject.getFieldB().getName());

        log.debug("type: {}, name: {}", annotationInject.getFieldC().getClass(),
                        annotationInject.getFieldC().getName());

        ctx.close();
    }
}
```

> 注：spring 中，先进行注解注入，然后才是 xml 注入，因此如果注入的目标相同，后者会覆盖前者。

### 自动装配

0. 准备代码

	```java
	@Component  // 声明为spring 组件 让自动扫描发现
	public class UserDao {
	    public List<User> getAllUsers(){
	        List list=new ArrayList<String>();
	        list.add("a");
	        list.add("b");
	        list.add("c");
	        return list;
	    }
	}
	```

	```java
	@Component // 声明为spring 组件 让自动扫描发现
	public class UserService{
	
	    private UserDao  userDao;
	
	    @Autowired
	    public UserService(UserDao userDao) {
	        this.userDao = userDao;
	    }
	
	    public List<User> userList(){
	        return userDao.getAllUsers();
	    }
	}
	```

1. 配置元数据

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context
	        http://www.springframework.org/schema/context/spring-context-4.2.xsd">
	
	    <!-- base-package 指定扫描包 -->
	    <context:component-scan base-package="com.shuai.springioc.dao" />
	    <context:component-scan base-package="com.shuai.springioc.service" />
	
	</beans>
	```

2. 实例化容器

	```java
	AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(UserConfig.class);
	```

	

3. 使用Bean

	```java
	UserService userService = (UserService) applicationContext.getBean("userService");
	        System.out.println(userService.userList());// 注意是 配置类中的方法名
	```



### IOC依赖注入

> 解决容器中Bean之间的依赖关系。

DI，是 Dependency Injection 的缩写，即依赖注入。依赖注入是 IoC 的最常见形式。依赖注入是手动或自动绑定的方式，无需依赖特定的容器或 API。

**容器全权负责组件的装配，它会把符合依赖关系的对象通过 JavaBean 属性或者构造函数传递给需要的对象**。

DI 是组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

理解 DI 的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”，那我们来深入分析一下：

- **谁依赖于谁：**当然是应用程序依赖于 IoC 容器；
- **为什么需要依赖：**应用程序需要 IoC 容器来提供对象需要的外部资源；
- **谁注入谁：**很明显是 IoC 容器注入应用程序某个对象，应用程序依赖的对象；
- **注入了什么**：就是注入某个对象所需要的外部资源（包括对象、资源、常量数据）。



> 这里的应用程序指的是：
>
> 	1. 我们项目构建从IOC容器中获取的bean 
>  	2. IOC容器中的Bean





<font color=ff00aa>解疑</font>

1. ![在这里插入图片描述](第二章-Spring-IOC.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nY29uZ3lpNDIw,size_16,color_FFFFFF,t_70.png)

	![image-20200924223522859](第二章-Spring-IOC.assets/image-20200924223522859.png)

	`Ioc容器`如何知道哪些是它管理的对象呢？这就需要`配置文件`，从示意图可以看出，当web容器启动的时候，`spring的全局bean的管理器会去xml配置文件中扫描的包下面获取到所有的类`，并根据你使用的注解，进行对应的封装，封装到`全局的bean容器`中进行管理，一旦容器初始化完毕，beanID以及bean实例化的类对象信息就全部存在了，现在我们需要在某个service里面调用另一个bean的某个方法的时候，我们只需要依赖注入进来另一个bean的Id即可，调用的时候，spring会去初始化完成的bean容器中获取即可，如果存在就把依赖的bean的类的实例化对象返回给你，你就可以调用依赖的bean的相关方法或属性等；





* 依赖查找： Dependency Lookup， DL，容器提供回调接口和上下文环境给组件，程序代码则需要提供具体的查找方式。比较典型的是依赖于 JNDI 系统的查找。

* 依赖注入： Dependency Injection， DI，程序代码不做定位查询，这些工作由容器自行完成。





## Bean

由 IoC 容器管理的那些组成你应用程序的对象我们就叫它 Bean。Bean 就是由 Spring 容器初始化、装配及管理的对象，除此之外，bean 就与应用程序中的其他对象没有什么区别了。那 IoC 怎样确定如何实例化 Bean、管理 Bean 之间的依赖关系以及管理 Bean 呢？这就需要配置元数据，在 Spring 中由 BeanDefinition 代表，后边会详细介绍，配置元数据指定如何实例化 Bean、如何组装 Bean 等。

### Bean 概述

一个 Spring 容器管理一个或多个 bean。这些 bean 根据你配置的元数据（比如 xml 形式）来创建。

Spring IoC 容器本身，并不能识别你配置的元数据。为此，要将这些配置信息转为 Spring 能识别的格式——BeanDefinition 对象。

#### 命名 Bean

指定 id 和 name 属性不是必须的。Spring 中，并非一定要指定 id 和 name 属性。实际上，Spring 会自动为其分配一个特殊名。如果你需要引用声明的 bean，这时你才需要一个标识。官方推荐驼峰命名法来命名。

#### 支持别名

可能存在这样的场景，不同系统中对于同一 bean 的命名方式不一样。 为了适配，Spring 支持 `<alias>` 为 bean 添加别名的功能。

```
<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
<alias name="subsystemA-dataSource" alias="myApp-dataSource" />
```

#### 实例化 Bean

**构造器方式**

```
<bean id="exampleBean" class="examples.ExampleBean"/>
```


