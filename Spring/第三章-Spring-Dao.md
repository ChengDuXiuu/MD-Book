## 简介

数据源、数据库连接池、JPA、jdbc、jdbcTemplate、Hibernate、Mybatis、Mybatis-Plus、SpringBoot-JPA  参考 ：https://github.com/YunaiV/SpringBoot-Labs#spring-boot-%E4%B8%93%E6%A0%8F



## JdbcTemplate

这里就不多介绍了，我不太喜欢这个框架。使用参考 ： https://www.iocoder.cn/Spring-Boot/JdbcTemplate/?github

`mybatis流程`

1.  定义实体类
2.  定义server层
3.  定义dao层
4.  定义映射xml文件【用来关联dao层方法以及关联对应的SQL语句】

`优点`

*   可以完全独立spring【注解开发用到aop】
*   只要SQL支持，他可以指定很复杂的SQL操作
*   xml中定义SQL 直观明了

`缺点`

*   最简单的查询任然需要定义xml文件【虽然有逆向工程】
*   每个与数据库操作都需要手撸SQL语句不够面向对象

`线程安全问题`

*   纯mybatis数据操作【就是sqlSession 连接  操作  释放 那一套】 是线程不安全的
*   但是spring 整合 mybatis后的 **mybatis-spring.jar**中的**SqlSessionTemplate** 是线程安全的 【以后再深究】

`事务问题`

*   默认开启事务，即关闭了自动提交

`jdbcTemplate流程`

1.  定义实体类
2.  定义server层
3.  定义dao层【其实现类继承 jdbcTemplate提供的 JdbcDaoSupport父类，可以使用其中的jdbcTemplate属性进行sql操作，任然需要写SQL语句】

`线程安全问题`

*   是线程安全的  【以后深究，先记着】

`事务问题`

*   默认是不开启事务的，执行后自动提交持久化

`mybatis和jdbcTemplate比较`

*   都差不多，都需要写SQL，jdbcTemplate封装了xml文件
*   jdbcTemplate 属于spring 生态，高度利用了spring IOC







## 事务

`介绍`

​	事务偏向安全管理级别，用来确保一个**工作单元全部执行**。比如我们去银行取钱  可以拆分为 1. 你账户余额扣除1000元，2. 然后ATM机吐出1000元。

只有这两个步骤全部执行，则你取钱成功，只要任意一个失败则回滚到你取钱前的状态。更形象的比喻为 转账。



`特性`

-   原子性（Atomicity）：事务是一个原子操作，由一系列动作组成。事务的原子性确保动作要么全部完成，要么完全不起作用。
-   一致性（Consistency）：一旦事务完成（不管成功还是失败），系统必须确保它所建模的业务处于一致的状态，而不会是部分完成部分失败。在现实中的数据不应该被破坏。
-   隔离性（Isolation）：可能有许多事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏【一般出现在处理并发的时候】。
-   持久性（Durability）：一旦事务完成，无论发生什么系统错误，它的结果都不应该受到影响，这样就能从任何系统崩溃中恢复过来。通常情况下，事务的结果被写到持久化存储器中。





`事务管理器接口 PlatformTransactionManager `

​	其主要用于完成事务的提交、回滚，及获取事务的状态信息。

**两个常用的实现类：**

*   DataSourceTransactionManager：使用 JDBC 或 iBatis 进行持久化数据时使用。
*    HibernateTransactionManager：使用 Hibernate 进行持久化数据时使用。  

**回滚方式：**

*   发生运行时异常时回滚
*   发生受查异常时提交。 不过，对于受查异常，程序员也可以手工设置其回滚方式。  

**异常与错误[Error]**

![image-20210302150215278](第三章-Spring-Dao.assets/image-20210302150215278.png)

*   Throwable 类是 Java 语言中所有错误或异常的超类。  
*   Error 是程序在运行过程中出现的无法处理的错误，比如 OutOfMemoryError、ThreadDeath、 NoSuchMethodError 等。当这些错误发生时，程序是无法处理（捕获或抛出）的， JVM 一般会终止线程。  
*   Exception 是编译或者运行时出现的一类错误，需要程序员去处理
*   Exception 分为 运行时异常    和     受查异常也叫编译异常
*   运行时异常， 是 RuntimeException 类或其子类 ， 即只有在运行时才出现的异常。  
*   受查异常，也叫编译时异常，即在代码编写时要求必须捕获或抛出的异常，若不处理，则无法通过编译。如 SQLException， ClassNotFoundException， IOException 等都属于受查异常。  
*   程序员在定义异常时，只要未明确声明定义的为 RuntimeException 的子类，那么定义的就是受查异常。  



`事务定义接口 TransactionDefinition`

​	事务定义接口 TransactionDefinition 中定义了事务描述相关的三类常量： 事务隔离级别、事务传播行为、事务默认超时时限， 及一种控制并发执行的事务对数据操作的规则。  

​	

**事务隔离级别** 【针对 特性：隔离性即并发处理事务】

​	在标准SQL（SQL 92）中定义了四种事务的隔离级别。这些常量均是以 ISOLATION_开头。即形如 ISOLATION_XXX。

*   DEFAULT： 采用 DB 默认的事务隔离级别。 MySql 的默认为 REPEATABLE_READ； Oracle默认为 READ_COMMITTED。
*   READ_UNCOMMITTED： 读未提交。 未解决任何并发问题。引起的问题包括：脏读，不可重复读和幻读。
*   READ_COMMITTED： 读已提交。解决脏读，存在不可重复读与幻读。引发的问题包括：不可重复读和幻读
*   REPEATABLE_READ： 可重复读。解决脏读、不可重复读，存在幻读。引发的问题包括：幻读
*   SERIALIZABLE： 串行化。不存在并发问题。  这种隔离级别下，可以避免脏读、不可重复读和幻读等问题，但是由于事务一个一个执行，所以性能就比较低。

>   其中隔离级别最宽松的是读未提交，最严格的是可串行化，当事务隔离级别较低时会引起一些数据问题（后文会讲解），当事务隔离级别设置为可串行化的时候，也就意味着事务的执行就类似于串行了，这个时候性能就会受到影响，所以在实际的业务中，是根据自己的需求来设置合理的事务的隔离级别，在性能和数据安全的两者之间找一个平衡点



**并发事务带来的问题**

>   事务隔离级别就是用来解决  事务 的隔离性  。即多个事务并发的问题

*   脏读

    脏读其实就是读到了其他事务回滚前的未提交的脏数据。简单来讲，两个事务 A，B同时操作某一个数据，A操作后提交保存至数据库，这个时候B读到了A修改后的值，但是A发生事务回滚，修改的值又恢复原样。

*   不可重复读

    不可重复读就是在一个事务多次相同的读取可能会读出不同的数据。

*   幻读

    其他事务在一个尚未提交的当前事务的读取的行的范围中插入新行或删除现有行，会对当前事务的对数据的读取产生幻象。

    >   脏读是读取到了提交后的数据，并且改数据之后发生回滚。即读取到了不存在的数据
    >
    >   幻读是读取到了不是最新的数据



**事务传播行为**

​	指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行。 例如：methodA事务方法调用methodB事务方法时，methodB是继续在调用者methodA的事务中运行呢，还是为自己开启一个新事务运行，这就是由methodB的事务传播行为决定的。定义了7个事务传播行为

*   REQUIRED： 指定的方法必须在事务内执行。若当前存在事务，就加入到当前事务中；

    ![image-20210302180509789](第三章-Spring-Dao.assets/image-20210302180509789.png)

    ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void methodA() {
     methodB();
    // do something
    }
     
    @Transactional(propagation = Propagation.REQUIRED)
    public void methodB() {
        // do something
    }
    ```

    >   单独调用methodB方法时，因为当前上下文不存在事务，所以会开启一个新的事务。 
    >   调用methodA方法时，因为当前上下文不存在事务，所以会开启一个新的事务。当执行到methodB时，methodB发现当前上下文有事务，因此就加入到当前事务中来。

*   SUPPORTS： 指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。

    ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void methodA() {
     methodB();
    // do something
    }
     
    // 事务属性为SUPPORTS
    @Transactional(propagation = Propagation.SUPPORTS)
    public void methodB() {
        // do something
    }
    ```

    >   单纯的调用methodB时，methodB方法是非事务的执行的。当调用methdA时,methodB则加入了methodA的事务中,事务地执行。

*   MANDATORY： 指定的方法必须在当前事务内执行，若当前没有事务，则直接抛出异常。

    ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void methodA() {
     methodB();
    // do something
    }
     
    // 事务属性为MANDATORY
    @Transactional(propagation = Propagation.MANDATORY)
    public void methodB() {
        // do something
    }
    ```

    >   当单独调用methodB时，因为当前没有一个活动的事务，则会抛出异常throw new IllegalTransactionStateException(“Transaction propagation ‘mandatory’ but no existing transaction found”);当调用methodA时，methodB则加入到methodA的事务中，事务地执行。

*   REQUIRES_NEW： 总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。需要使用 JtaTransactionManager作为事务管理器。 

    ![image-20210302190909755](第三章-Spring-Dao.assets/image-20210302190909755.png)

    ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void methodA() {
    doSomeThingA();
    methodB();
    doSomeThingB();
    // do something else
    }
     
     
    // 事务属性为REQUIRES_NEW
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void methodB() {
        // do something
    }
    ```

    **当调用方法A时，相当于如下代码**

    ```java
    main(){
        TransactionManager tm = null;
        try{
            //获得一个JTA事务管理器
            tm = getTransactionManager();
            tm.begin();//开启一个新的事务
            Transaction ts1 = tm.getTransaction();
            doSomeThing();
            tm.suspend();//挂起当前事务
            try{
                tm.begin();//重新开启第二个事务
                Transaction ts2 = tm.getTransaction();
                methodB();
                ts2.commit();//提交第二个事务
            } Catch(RunTimeException ex) {
                ts2.rollback();//回滚第二个事务
            } finally {
                //释放资源
            }
            //methodB执行完后，恢复第一个事务
            tm.resume(ts1);
            doSomeThingB();
            ts1.commit();//提交第一个事务
        } catch(RunTimeException ex) {
            ts1.rollback();//回滚第一个事务
        } finally {
            //释放资源
        }
    }
    ```

    >   在这里，我把ts1称为外层事务，ts2称为内层事务。从上面的代码可以看出，ts2与ts1是两个独立的事务，互不相干。Ts2是否成功并不依赖于 ts1。如果methodA方法在调用methodB方法后的doSomeThingB方法失败了，而methodB方法所做的结果依然被提交。而除了 methodB之外的其它代码导致的结果却被回滚了

*   NOT_SUPPORTED： 总是非事务地执行，并挂起任何存在的事务。使用PROPAGATION_NOT_SUPPORTED,也需要使用JtaTransactionManager作为事务管理器。 

    ![image-20210302191314624](第三章-Spring-Dao.assets/image-20210302191314624.png)

    

*   NEVER： 指定的方法不能在事务环境下执行，若当前存在事务，就直接抛出异常。  即如果在一个事务方法中调用指定NEVER事务类型的方法则会报错

*   NESTED： 指定的方法必须在事务内执行。若当前存在事务，则在嵌套事务内执行；若当前没有事务，则创建一个新事务。  

    ![image-20210302191428740](第三章-Spring-Dao.assets/image-20210302191428740.png)





**事务默认超时时限**

​	常量 TIMEOUT_DEFAULT 定义了事务底层默认的超时时限，及不支持事务超时时限设置的 none 值。注意，事务的超时时限起作用的条件比较多，且超时的时间计算点较复杂。所以，该值一般就使用默认值即可  