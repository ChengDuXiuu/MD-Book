## JDK Version1.1
<code><font color=#f759ab size=4>于1997-02-19发布</font></code>

1. 引入JDBC
2. 支持内部类
3. 引入 Java Bean
4. 引入RMI
5. 引入反射

## JDK Version1.2
<code><font color=#f759ab size=4>于1998-12-08发布</font></code>

1. 引入集合（ Collection）框架
2. 对字符串常量做内存映射
3. 引入 JIT（ Just In Time）编译器
4. 引入对打包的 Java 文件进行数字签名
5. 引入控制授权访问系统资源的策略工具； 引入 JFC（ Java Foundation Classes），包括 Swing 1.0、拖放和 Java 2D
类库
6. 引入 Java 插件
7. 在 JDBC 中引入可滚动结果集、 BLOB、 CLOB、批量更新和用户自定义类型
8. 在 Applet 中添加声音支持

## JDK Version1.3(红隼)
<code><font color=#f759ab size=4>于2000-05-08发布</font></code>

1. 引入 Java Sound API
2. jar 文件索引
3. 对 Java 的各个方面都做了大量优化和增强。

## JDK Version1.4(隼)
<code><font color=#f759ab size=4>于2004-02-06 发布</font></code>

1. XML 处理
2. Java 打印服务
3. 引入 Logging API
4. 引入 Java Web Start
5. 引入 JDBC 3.0 API
6. 引入断言
7. 引入 Preferences API
8. 引入链式异常处理
9. 支持 IPv6
10. 支持正则表达式
11. 引入 Image I/O slot machine API

## JDK Version1.5(老虎)
<code><font color=#f759ab size=4>于2004-09-30 发布</font></code>

1. 引入泛型
2. 增强循环，可以使用迭代方式
3. 自动装箱与自动拆箱
4. 类型安全的枚举
5. 可变参数
6. 静态引入
7. 元数据（注解）
8. 引入 Instrumentation

## JDK Version1.6(野马)
<code><font color=#f759ab size=4>于2006-12-11 发布</font></code>

1. 支持脚本语言
2. 引入 JDBC 4.0 API
3. 引入 Java Compiler API
4. 可插拔注解
5. 增加对 Native PKI(Public Key Infrastructure)、 Java GSS(Generic
Security Service)、 Kerberos 和 LDAP(Lightweight Directory Access Protocol)的支持
6. 继承 Web Services； 做了很多优化

## JDK Version1.7(海豚)
<code><font color=#f759ab size=4>于2011-07-28  发布</font></code>

1. switch 语句块中允许以字符串作为分支条件
2. 在创建泛型对象时应用类型推断
3. 在一个语句块中捕获多种异常
4. 支持动态语言
5. 支持 try-with-resources
6. 引入 Java NIO.2 开发包； 数值类型可以用 2 进制字符串表示，并且可以在字符串表示中添加下划线
7. 钻石型语法
8. null 值的自动处理

## JDK Version1.8(海豚)
<code><font color=#f759ab size=4>于2014-3-14  发布</font></code>

1. Lambda 表达式
2. Pipelines 和 Streams
3. Date 和 Time API
4. Default 方法
5. Type 注解 Nashhorn JavaScript 引擎
6. 并发计数器
7. Parallel 操作
8. 移除 PermGen Error
9. TLS SNI

## Java版本
| 版本 | 体积/性能 | 备注 |
| :----: | :----: | :----: |
| Java ME | 瘦身版(小) | jvm与下两个版本不兼容 |
| Java SE | 标准版(中) | jvm可与企业版兼容 |
| Java EE | 企业版(大) | jvm可与标准版兼容 |

## 环境变量
1. classpath

  作用是指定类搜索路径,要使用已经编写好的类，前提当然是能够找到它们了，JVM就是通过CLASSPTH来寻找类的。此环境变量建议不加入到系统环境中(防止污染系统环境变量)，可以在启动jvm(命令：<code>java</code>)时指定参数(<font color=#ad6800>Java所有程序都是通过参数来调用</font>)。
        EG：java -classpath .;URL1;URL2;..; className
2. JAVA_HOME

  JDK路径
3. path

  系统环境变量，可在任意目录执行可执行文件(先在当前目录搜寻可执行文件) ，在path中加入JAVA_HOME/bin就是为了在任意目录中运行bin文件夹中的<font color=#ad6800>java(jvm)、javac(java编译器)、jar(包)、javadoc(生成文档)、jdb(Java调试器)、javap -c className(反编译class)</font>等可执行文件

## 无第三方依赖包打包
1. 找到包含所有class文件的目录<font color=#ad6800>在class的package上级目录即可，例如：包名demo下有若干个class，只需要找到demo父级目录即可</font>
2. 压缩目录后修改文件格式为jar即可。
3. 如果有其他第三方还是使用<code>Maven</code>吧。因为需要编写.mf文件
