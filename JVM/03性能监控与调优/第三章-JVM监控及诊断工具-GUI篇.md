`引用：`

使用上一章命令行工具或组合能帮您获取目标Java应用性能相关的基础信息，但它们存在下列局限：

- 1．无法获取方法级别的分析数据，如方法间的调用关系、各方法的调用次数和调用时间等（这对定位应用性能瓶颈至关重要）。
- 2．要求用户登录到目标 Java 应用所在的宿主机上，使用起来不是很方便。
- 3．分析数据通过终端输出，结果展示不够直观。

为此，JDK提供了一些内存泄漏的分析工具，如jconsole，jvisualvm等，用于辅助开发人员定位问题，但是这些工具很多时候并不足以满足快速定位的需求。所以这里我们介绍的工具相对多一些、丰富一些。



## 图形化工具

### 1、JDK自带工具

* jconsole

	功能简单，支持查看Java应用程序的运行概况、监控堆信息、永久区（或元空间）使用情况、类加载情况等

	`检测死锁示例`

	![image-20210711155545129](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711155545129.png)

	![image-20210711155556851](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711155556851.png)

* Visual VM

	相较jconsole强大。支持的功能也更为丰富，并且支持插件安装

	`远程连接示例`

	![image-20210711160535927](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711160535927.png)

	`导出dump文件示例`

	![image-20210711161418759](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711161418759.png)

	`检测死锁示例`

	![image-20210711162013604](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711162013604.png)

* JMC

	内置Java Flight Recorder。能够以极低的性能开销收集Java虚拟机的性能数据。

### 2、第三方工具

* MAT

	是基于Eclipse的内存分析工具，是一个快速、功能丰富的Java heap分析工具，它可以帮助我们查找内存泄漏和减少内存消耗。<font color=ff00aa>主要用于hump文件分析</font>

	通过对hump文件分析可以获取如下内存信息：

	- 所有的对象信息，包括对象实例、成员变量、存储于栈中的基本类型值和存储于堆中的其他对象的引用值。
	- 所有的类信息，包括classloader、类名称、父类、静态变量等
	- GCRoot到所有的这些对象的引用路径
	- 线程信息，包括线程的调用栈及此线程的线程局部变量（TLS）

	`查看dump文件示例`

	* 离线dump查看

		![image-20210711170034105](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711170034105.png)

		![image-20210711170331244](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711170331244.png)

	* 在线内存分析

		![image-20210711170448678](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711170448678.png)

		![image-20210711170504977](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711170504977.png)

* JProfiler

  商业软件，需要付费。功能强大。

  **特点：**

  - 使用方便、界面操作友好（简单且强大）
  - 对被分析的应用影响小（提供模板）
  - CPU，Thread，Memory分析功能尤其强大
  - 支持对jdbc，noSql，jsp，servlet，socket等进行分析
  - 支持多种模式（离线，在线）的分析
  - 支持监控本地、远程的JVM
  - 跨平台，拥有多种操作系统的安装版本

  **主要功能：**

  - 1-方法调用：对方法调用的分析可以帮助您了解应用程序正在做什么，并找到提高其性能的方法
  - 2-内存分配：通过分析堆上对象、引用链和垃圾收集能帮您修复内存泄露问题，优化内存使用
  - 3-线程和锁：JProfiler提供多种针对线程和锁的分析视图助您发现多线程问题
  - 4-高级子系统：许多性能问题都发生在更高的语义级别上。例如，对于JDBC调用，您可能希望找出执行最慢的SQL语句。JProfiler支持对这些子系统进行集成分析

  **打开方式介绍**

  ![image-20210712225650676](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712225650676.png)

  **数据采集方式**

  ![image-20210712230057254](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230057254.png)

  **功能分类**

  ![image-20210712230305304](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230305304.png)

  **遥感监测-内存**

  ![image-20210712230547474](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230547474.png)

  **遥感监测-垃圾回收分析**

  ![image-20210712230710079](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230710079.png)

  **遥感监测-加载类情况**

  ![image-20210712230757855](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230757855.png)

  **遥感监测-线程分析**

  ![image-20210712230918144](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712230918144.png)

  **遥感监测-CPU分析**

  ![image-20210712231032425](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712231032425.png)

  **内存视图-分类**

  ![image-20210712231503542](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712231503542.png)

  **内存视图-所有对象**

  ![image-20210712231644974](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712231644974.png)

  ![image-20210712232310973](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712232310973.png)

  > 从这个图中可以分析出：
  >
  > * 哪些对象创建频繁   ---- >  如果实例个数多且总体容量大则符合此标准
  > * 哪些对象是大对象   ---- >  如果实例个数不多，但是总体容量很大则符合此标准
  > * 定位到内存泄漏情况   ---- >  从内存回收器情况进行分析

  **内存视图-定位内存泄漏**

  ![image-20210712233151855](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712233151855.png)

  > 每次垃圾回收时，内存稍微减少，总体趋势逐步上升则一定会发生内存溢出。内存分析有溢出情况如何进一步定位？如下

  **内存视图-记录对象（进一步排查内存泄漏问题）**

  ![image-20210712233756307](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712233756307.png)

  ![image-20210712234138743](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712234138743.png)

  ![image-20210712234414384](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712234414384.png)

  执行GC后对象即活跃对象是这样的：

  ![image-20210712234609537](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712234609537.png)

  GC后对象情况如下图：

  ![image-20210712234644295](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712234644295.png)

  ![image-20210712234655013](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712234655013.png)

  > gc后对象都可以被回收，说明对象都正常没有内存泄漏问题，下图举例对象泄漏情况

  ![image-20210712235127046](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210712235127046.png)

  **堆视图-发现大对象引用连**

  ![image-20210713220653171](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713220653171.png)

  ![image-20210713220822817](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713220822817.png)

  > 右键查看堆信息

  ![image-20210713220905619](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713220905619.png)

  ![image-20210713220924070](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713220924070.png)

  > 发现引用连

  ![image-20210713221001531](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713221001531.png)

  ![image-20210713221116645](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713221116645.png)

  **CPU视图--方法树**

  ![image-20210713222107076](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713222107076.png)

  > 根据CPU视图，能够分析出哪些方法执行时间过长以及调用次数很多。方法执行时间长的就占用CPU时间长

  **CPU视图--方法情况**

  ![image-20210713222317508](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210713222317508.png)

  

  **线程视图**

  > 线程视图这块最关心的是：
  >
  > 1. web容器的线程最大数，比如Tomcat线程数量应略大于最大并发数
  > 2. 线程阻塞
  > 3. 线程死锁

  ![线程死锁](第三章-JVM监控及诊断工具-GUI篇.assets/线程死锁.gif)

  > 随着时间的推移，线程一二，始终阻塞状态，发生死锁

  

  

  

* Arthas

  阿里巴巴开源的Java诊断工具。功能十分强大

  > 为什么有了上面的三款软件 MAT、jvisualvm、JProfiler还要有 Arthas，因为通常程序是部署下服务器上的，如果想要进行调优还需要远程连接，这样就涉及到网关防火墙等一系列配置，麻烦。

* JMC（java mission control）

    实时监测jvm运行时状态

    ![image-20210715135438471](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210715135438471.png)

    ![image-20210715140029847](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210715140029847.png)

* Btrace

	Java运行时追踪工具，可以在不停机的情况下，跟中指定的方法调用，构造函数调用和系统内存等信息



### 3、其他工具

*   火焰图(Flame Graphs)

    >   非常直观的显示出调用栈中的CPU消耗瓶颈，用于发现接口的性能瓶颈并改善

*   Tprofiler

    >   用于在海量数据中，快速定位性能代码，解决性能瓶颈，最重要特性就是能够在指定时间内统计出你jvm的top method，而这些top method极有可能就是造成性能瓶颈的元凶



## 工具对比

* 远程连接
* 死锁
* 内存oom分析
* CPU飙高分析
* 普通异常分析

|                  | JvisualVM                      | Jprofiler                                  | Arthas                                     |
| ---------------- | ------------------------------ | ------------------------------------------ | ------------------------------------------ |
| 远程连接         | 远程配置Tomcat并重启           | 远程配置Tomcat并重启                       | Web Console或者Telnet远程                  |
| 死锁             | 支持并定位到具体线程具体代码   | 支持并定位到具体线程具体代码               | 支持(仅限synchronized)                     |
| 内存OOM分析      | 定位到哪个线程和哪个对象(很泛) | 可从内外引用定位到GC Roots                 | 可从内外引用定位到GC Roots                 |
| CPU飙高分析      | 定位到线程和方法               | 定位到线程和方法以及方法引用链以及执行时间 | 定位到线程和方法以及方法引用链以及执行时间 |
| 数据处理异常分析 |                                |                                            |                                            |
| 方法调用链       |                                |                                            |                                            |
| 代码热部署       |                                |                                            |                                            |





### 死锁

`代码`

```java
public class ThreadBlock {
    public static Object object1 = new Object();
    public static Object object2 = new Object();

    public static void main(String[] args) {
        thread1();
        thread2();
    }
    @SuppressWarnings(value = "all")
    public static void thread1(){
        new Thread(()->{
            synchronized (object1){
                System.out.println("线程  "+Thread.currentThread().getName()+"获取到 object1 的锁");
                System.out.println("执行代码 。。。。");
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (object2){
                    System.out.println("线程  "+Thread.currentThread().getName()+"获取到 object2 的锁");
                    System.out.println("执行代码 。。。。");
                }
            }
        },"thread - 1").start();
    }
    @SuppressWarnings(value = "all")
    public static void thread2(){
        new Thread(()->{
            synchronized (object2){
                System.out.println("线程  "+Thread.currentThread().getName()+"获取到 object2 的锁");
                System.out.println("执行代码 。。。。");
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (object1){
                    System.out.println("线程  "+Thread.currentThread().getName()+"获取到 object1 的锁");
                    System.out.println("执行代码 。。。。");
                }
            }
        },"thread - 2").start();
    }

}

```



#### 1、JvisualVM

![image-20210724223020011](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223020011.png)

![image-20210724223103836](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223103836.png)



#### 2、Jprofiler

![image-20210724223148812](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223148812.png)

![image-20210724223222572](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223222572.png)



#### 3、Arthas

1. 查看所有线程

	```bash
	thread --all
	```

	![image-20210724223440597](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223440597.png)

	或者

	```bash
	thread -b
	```

	![image-20210724223634920](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223634920.png)

2. 查看阻塞线程信息

	![image-20210724223806053](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724223806053.png)



### 内存OOM分析

`代码`

```java
public class MemoryLeak {
    public static void main(String[] args){
        new Thread(()->{
            try {
                threadStart();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
    public static void threadStart () throws InterruptedException {
        while (true){
            ArrayList beanList = new ArrayList();
            for (int i = 0; i < 500; i++) {
                Bean data = new Bean();
                data.list.add(new byte[1024*10]);//10kb
                beanList.add(data);
            }
            TimeUnit.MILLISECONDS.sleep(500);
        }
    }
}
class Bean{
    int size=10;
    String info = "hello world";
    static ArrayList list = new ArrayList();
}
```

`jvm参数`

```bash
-Xms2g -Xmx2g -XX:NewSize=50m -XX:MaxNewSize=50m -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/Users/mac/auto.hprof
```



#### 1、JvisualVM

1. 定位

![image-20210724224159674](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724224159674.png)

> 持续增高，不健康



2. 内存泄漏寻找大对象

	![image-20210724224301589](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724224301589.png)

![image-20210724224333869](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724224333869.png)

3. 堆 dump 分析

	![image-20210724224608980](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724224608980.png)

	> 双击大对象，查看其具体信息

	![image-20210724224717193](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724224717193.png)

	> 500多个10264(10kb)的的实例

> <font color=ff00aa>只能定位到这里，挺鸡肋，除非你是实实在在的自定义对象</font>



#### 2、Jprofiler

1. 定位

![image-20210724225200371](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724225200371.png)

> 指定不健康



2. 分析大对象

	![image-20210724225145181](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724225145181.png)



3. 进一步分析内存泄漏对象

	![image-20210724225515307](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724225515307.png)

	> 执行GC后 byte[] 对象并没有回收一点点，因此此对象一定存在内存泄漏问题，着重搞他

4. 内存泄漏对象的内存分析

	![image-20210724225732365](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724225732365.png)

	![image-20210724225745850](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724225745850.png)

	![image-20210724230021653](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724230021653.png)

	![image-20210724230147640](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724230147640.png)

	> Bean对象太多导致

#### 3、Arthas

1. 定位

	```bash
	dashboard -i 2000 -n 5
	```

	![image-20210724231827603](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724231827603.png)

	![image-20210724231852285](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724231852285.png)

	![image-20210724231922422](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724231922422.png)

	![image-20210724231933122](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724231933122.png)

	> heap 的内存使用率一直提升。

2. 导出dump文件

	```bash
	heapdump /tmp/dump.hprof
	```

3. 使用MAT或者JProfiler打开

	![image-20210724234738799](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724234738799.png)

	![image-20210724234803319](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724234803319.png)

	![image-20210724234840489](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210724234840489.png)



### CPU飙高分析

`代码`

```java
public class CpuHitgh {
    public static void main(String[] args) {
        threadStart1();
        threadStart2();
    }
    @SuppressWarnings(value = "all")
    public static void threadStart1(){
        new Thread(()->{
            while (true){
                System.out.println("执行代码。。。。");
            }
        }).start();
    }
    @SuppressWarnings(value = "all")
    public static void threadStart2(){
        new Thread(()->{
            while (true){
                System.out.println("执行代码。。。。");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```



#### 1、Jvisualvm

* 粗略怀疑某些方法

	![image-20210725175911327](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725175911327.png)

* 定位消耗CPU高的线程

	![image-20210725175945673](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725175945673.png)

	> 如果线程没有赋予名字，则抓瞎。当然也可以通过如下获取线程对应代码

	![image-20210725180058414](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725180058414.png)

	![image-20210725180132996](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725180132996.png)



#### 2、JProfiler

![image-20210725180614714](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725180614714.png)

> 通过方法热点排序。可以排查出哪些方法占用了太多的CPU资源



#### 3、Arthas

1. 定位到具体线程

	```bash
	dashboard -i 2000 -n 5
	或者
	thread -n 5
	```

	![image-20210725182056375](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210725182056375.png)

2. 然后通过tt和watch等监听方法







## 案例分析

### 1、Tomcat堆溢出分析

> Tomca本身也是由Java实现，因此我们可以像监控Java进程一样监控Tomcat进程

1. 发生OOM，首先查看大对象

	![image-20210711230103706](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711230103706.png)

2. 查看其外引用和内引用

	![image-20210711230138477](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711230138477.png)

	![image-20210711230235769](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711230235769.png)

	![image-20210711230509629](第三章-JVM监控及诊断工具-GUI篇.assets/image-20210711230509629.png)

3. 可知，是由于线程池中阻塞队列造成的