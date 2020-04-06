IO是程序实现输入和输出操作的功能统称，利用IO操作机制可以实现磁盘数据、网络数据以及内存数据的输入输出处理，在Java中提供的IO操作组件依据面向对象的设计结构进行整体IO结构处理，在开发者理解了面向对象的设计之后就可以非常轻松的掌握整个IO设计的框


整个 IO 包中实际上需要的就是五个类和一个接口：File、OutputStream、InputStream、Writer、Reader；Serializable。

Java IO 是一套Java用来读写数据（输入和输出）的API。大部分程序都要处理一些输入，并由输入产生一些输出。Java为此提供了java.io包





### File

File 类在整个 IO 包中是唯一一个与文件本身有关的操作类.

```java
package file;

import cn.hutool.log.Log;
import cn.hutool.log.LogFactory;

import java.io.File;
import java.io.FileFilter;

/**
 * TODO:<p> File类简单操作 <p/>
 *
 * @package: file
 * @Author mac
 * @Date 2020/3/28 6:22 下午
 * @Version V1.0
 **/
public class Demo1 {
    private static Log log= LogFactory.get();
    public static void main(String[] args) {
        File file=new File("/Library/apache-maven-3.6.3/");//File.separator获取不同操作系统的路径分隔符
        findJavaFile(file);

    }
    public static void findJavaFile(File file) throws NullPointerException{
        File[] files = file.listFiles(new FileFilter() {
            @Override
            public boolean accept(File file) {
                if(file.isDirectory()){
                    findJavaFile(file);
                }else{
                    if (file.getName().endsWith(".jar")) {
                        return true;
                    }
                }
                return false;
            }
        });
        for (File file1 : files) {
            if(file1.isFile()){
                log.info(file1.getAbsolutePath()+file1.getName();
                i++;
            }else {
                findJavaFile(file1);
            }
        }
    }
}

```

### 文件操作流

File 类本身是与文件操作有关，但是如果要想操作内容则必须使用字节流或字符流完成，但是不管是使用何种的输入
输出流，其基本的操作原理是一样的（以文件流为准）

1、 使用 File 类找到一个文件

2、 通过字节流或字符流的子类进行对象的实例化

3、 进行读或写的操作

4、 关闭字节或字符流

由于流的操作属于资源操作，所以在操作的最后一定要关闭以释放资源

* 字节流：OutputStream、InputStream
* 字符流：Writer、Reader

#### 1、字节输出流


    关闭流：public void close() throws IOException

    写一组数据：public void write(byte[] b) throws IOException

    写一个数据：public void write(int b) throws IOException

    构造：public FileOutputStream(File file) throws FileNotFoundException > > 覆盖

    构造：public FileOutputStream(File file,boolean append) throws FileNotFoundException > > 追加    

但是要想为 OutputStream 实例化，且进行文件操作的话，就要使用 FileOutputStream 子类。

#### 2、字节输入流

    关闭：public void close() throws IOException

    读取一个字节：public abstract int read() throws IOException

    读取一组内容：public int read(byte[] b) throws IOException

```java
File file=new File("/Library/apache-maven-3.6.3/README.txt");
InputStream inputStream=null;
byte[] data=new byte[(int) file.length()];//容量一定要大于读取的文件大小
inputStream=new FileInputStream(file);
int temp=0;
int len=0;
try {
    while ((temp=inputStream.read())!=-1){
        data[len++]= (byte) temp;
    }
}finally {
    inputStream.close();
}

System.out.println(new String(data,0,len));
```
```Java
File file=new File("/Library/apache-maven-3.6.3/README.txt");
InputStream inputStream = null;
byte[] chars=new byte[8000];
try {
    if(!file.exists()){
        try {
            file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    try {
        inputStream=new FileInputStream(file);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
    int len=inputStream.read(chars);
}finally {
    inputStream.close();
}
System.out.println(new String(chars,0,(int)file.length()));
```

#### 3、字符输出流

    Writer out = new FileWriter(file);

#### 4、字符输入流

    Reader read = new FileReader(file);

#### 文件字符流与字节流区别

>字符流操作的时候会使用到缓冲区，而字节流操作的时候是不会使用到缓冲区的。
在输出的时候，OutputStream 类即使最后没有关闭内容也可以输出。但是如果是 Writer 的话，则如果不关闭，最后一条内容是无法输出的，因为所有的内容都是保存在了缓冲区之中，每当调用了 close()方法就意味着清空缓冲区了。那么可以证明字符流确实使用了缓冲区：
* 字节流：程序 > 文件
* 字符流：程序 > 缓冲区 > 文件

>如果现在字符流即使不关闭也可以完成输出的话，则必须强制性清空缓冲区：

* 方法：public void flush() throws IOException

>两者相比，肯定使用字节流更加的方便，而且在程序中像图片、MP3 等都是采用字节的方式的保存，那么肯定字节流会比字符流使用的更广泛。
但是需要说明的是，如果要是想操作中文的话，字符流肯定是最好使的


### 内存操作流

上述操作流是以文件的输入输出为主的，在用文件流处理的时候操作的终端就是磁盘上的文件内容。但是如果现在将输入输出的位置一改变，改变成了内存，那么就称为内存操作流。使用 ByteArrayInputStream 完成内存的输入操作，而使用 ByteArrayOutputStream 完成内存的输出操作。

<font color=ffaa00>对于此时的代码实质上就是在内存里面专门又开辟了一块新的空间进行数据的IO处理，本次实现了IO处理但是又没有产生任何的文件，所以那些临时的操作文件就可以基于IO的形式完成，可以采用内存流处理。</font>

```Java
InputStream input=new ByteArrayInputStream("hello world".getBytes());
OutputStream output=new ByteArrayOutputStream();
int temp = 0;
while ((temp = input.read()) != -1) {
    output.write(temp);
}
String str = output.toString(); // 取出内容
input.close() ;
output.close() ;
System.out.println(str) ;
```

### 打印流

    打印流只提供输出流包括字节输出流以及字符输出流。相较于上诉输出流它提供了更加强大的功能，支持几乎所有数据类型的数据进行输出，并且还提供了类似于c的printf格式化输出的方式。

    同样，System.out底层也是调用的打印流

```Java
OutputStream outputStream=new FileOutputStream(new File("/Library/apache-maven-3.6.3/README.txt"),true);
PrintStream printStream=new PrintStream(outputStream);
printStream.printf("姓名：%s，年龄：%d，成绩：%5.2f", "张三15", 15, 79.5);
outputStream.close();
printStream.close();
```

### Scanner
使用 Scanner 可以方便的完成各种字节输入流的输入，支持各种数据类型以及正则表达式
```Java
//屏幕输入
Scanner scan = new Scanner(System.in);
System.out.print("请输入日期：");
if (scan.hasNext("\\d{4}-\\d{2}-\\d{2}")) { // 现在有内容
    String str = scan.next("\\d{4}-\\d{2}-\\d{2}");
    Date date = null;
    try {
        date = new SimpleDateFormat("yyyy-MM-dd").parse(str);
    } catch (ParseException e) {
        e.printStackTrace();
    }
    System.out.println(date);
}
//文件输入
Scanner scan = new Scanner(new FileInputStream(new File("/Library/apache-maven-3.6.3/README.txt")));
scan.useDelimiter("\n"); // 将换行作为分隔符
while (scan.hasNext()) {
    String str = scan.next();
    System.out.print(str);
}
```
### io结构图

![io体系结构图](/assets/io体系结构图.jpg)![io体系分类图](/assets/io体系分类图.png)

### 对象序列化

为什么要对象序列化？

    当Java类运行在同一块jvm划分的内存中则不需要对象序列化，因为都能找得到，如果你想要调用不同jvm下的对象，或者是你使用rmi远程调用其他电脑中的Java对象又如何呢？因此使用对象序列化将对象变为二进制数据，然后由调用方反序列化为Java对象，然后使用其方法或属性。

```Java
//序列化对象
Shop shop=new Shop();
shop.setId(12);
shop.setName("序列化");
shop.setName("789");

ObjectOutputStream outputStream=new ObjectOutputStream(new FileOutputStream("/Library/apache-maven-3.6.3/shop.ini"));
outputStream.writeObject(shop);
outputStream.close();
```    
```java
//反序列化
Shop shop=new Shop();
shop.setId(12);
shop.setName("序列化");
shop.setName("789");

ObjectOutputStream outputStream=new ObjectOutputStream(new FileOutputStream("/Library/apache-maven-3.6.3/shop.ini"));
outputStream.writeObject(shop);
outputStream.close();

```
>如果不想序列化对象中某个属性，则使用transient修饰即可


<font size=5>java中io系统可以分为Bio，Nio，Aio三种io模型</font>

>关于Bio，我们需要知道什么是同步阻塞IO模型，Bio操作的对象：流，以及如何使用Bio进行网络编程，使用Bio进行网络编程的问题。<font color=ffaa00>适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择。</font>


>关于Nio，我们需要知道什么是同步非阻塞IO模型，什么是多路复用Io模型，以及Nio的Buffer,Channel,Selector的概念，以及如何使用Nio进行网络编程。<font color=ffaa00>适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂。</font>

>关于Aio，我们需要知道什么是异步非阻塞IO模型，Aio可以使用几种方式实现异步操作，以及如何使用Aio进行网络编程。<font color=ffaa00>使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。</font>

<table>
    <th>
        <tr>
            <td>组合方式</td>
            <td>性能分析</td>
        </tr>
    </th>
    <tbody>
        <tr>
            <td>同步阻塞</td>
            <td>最常用的一种用法，使用也是最简单的，但是 I/O 性能一般很差，CPU 大部分在空闲状态。</td>
        </tr>
        <tr>
            <td>同步非阻塞</td>
            <td>提升 I/O 性能的常用手段，就是将 I/O 的阻塞改成非阻塞方式，尤其在网络 I/O 是长连接，同时传输数据也不是很多的情况下，提升性能非常有效。 这种方式通常能提升 I/O 性能，但是会增加CPU 消耗，要考虑增加的 I/O 性能能不能补偿 CPU 的消耗，也就是系统的瓶颈是在 I/O 还是在 CPU 上。</td>
        </tr>
        <tr>
        <td>异步阻塞</td>
        <td>这种方式在分布式数据库中经常用到，例如在网一个分布式数据库中写一条记录，通常会有一份是同步阻塞的记录，而还有两至三份是备份记录会写到其它机器上，这些备份记录通常都是采用异步阻塞的方式写 I/O。异步阻塞对网络 I/O 能够提升效率，尤其像上面这种同时写多份相同数据的情况。</td>
        </tr>
        <tr>
        <td>异步非阻塞</td>
        <td>这种组合方式用起来比较复杂，只有在一些非常复杂的分布式情况下使用，像集群之间的消息同步机制一般用这种 I/O 组合方式。如 Cassandra 的 Gossip 通信机制就是采用异步非阻塞的方式。它适合同时要传多份相同的数据到集群中不同的机器，同时数据的传输量虽然不大，但是却非常频繁。这种网络 I/O 用这个方式性能能达到最高。</td>
        </tr>
    </tbody>
</table>

###<code><font color=ff00ff >BIO</font></code>

BIO是同步阻塞IO，JDK1.4之前只有这一个IO模型，BIO操作的对象是``流``，一个线程只能处理一个流的IO请求，如果想要同时处理多个流就需要使用多线程

流包括字符流和字节流，流从概念上来说是一个连续的数据流。当程序需要读数据的时候就需要使用输入流读取数据，当需要往外写数据的时候就需要输出流

``阻塞IO模型``


在Linux中，当应用进程调用recvfrom方法调用数据的时候，如果内核没有把数据准备好不会立刻返回，而是会经历等待数据准备就绪，数据从内核复制到用户空间之后再返回，这期间应用进程一直阻塞直到返回，所以被称为阻塞IO模型

``流``

BIO中操作的流主要有两大类，字节流和字符流，两类根据流的方向都可以分为输入流和输出流.按照类型和输入输出方向可分为：

输入字节流：InputStream
输出字节流：OutputStream
输入字符流：Reader
输出字符流：Writer

>字节流主要用来处理字节或二进制对象，字符流用来处理字符文本或字符串

>使用InputStreamReader可以将输入字节流转化为输入字符流
Reader reader  =  new InputStreamReader(inputStream);

>使用OutputStreamWriter可以将输出字节流转化为输出字符流
Writer writer = new OutputStreamWriter(outputStream)

我们可以在程序中通过InputStream和Reader从数据源中读取数据，然后也可以在程序中将数据通过OutputStream和Writer输出到目标媒介中

#### 1、原始类

1. 输入字节流：InputStream

    ```Java
    public static void main(String[] args) throws Exception{
        File file = new File("D:/a.txt");
        InputStream inputStream = new FileInputStream(file);
        byte[] bytes = new byte[(int) file.length()];
        inputStream.read(bytes);
        System.out.println(new String(bytes));
        inputStream.close();
    }
    ```
2. 输入字符流：Reader

    ```Java
    public static void main(String[] args) throws Exception{
        File file = new File("D:/a.txt");
        Reader reader = new FileReader(file);
        char[] bytes = new char[(int) file.length()];
        reader.read(bytes);
        System.out.println(new String(bytes));
        reader.close();
    }
    ```

3. 输出字节流：OutputStream

    ```Java
    public static void main(String[] args) throws Exception{
        String var = "hai this is a test";
        File file = new File("D:/b.txt");
        OutputStream outputStream = new FileOutputStream(file);
        outputStream.write(var.getBytes());
        outputStream.close();
    }
    ```
4. 输出字符流：Writer

    ```Java
    public static void main(String[] args) throws Exception{
        String var = "hai this is a test";
        File file = new File("D:/b.txt");
        Writer writer = new FileWriter(file);
        writer.write(var);
        writer.close();
    }
    ```

#### 2、实现类

1. BufferedInputStream

    在使用InputStream的时候，都是一个字节一个字节的读或写，而BufferedInputStream为输入字节流提供了缓冲区，读数据的时候会一次读取一块数据放到缓冲区里，当缓冲区里的数据被读完之后，输入流会再次填充数据缓冲区，直到输入流被读完，有了缓冲区就能够提高很多io速度

    ```Java
    /**
     * inputStream 输入流
     * 1024 内部缓冲区大小为1024byte
     */
    BufferedInputStream bufferedInputStream = new BufferedInputStream(inputStream,1024);
    ```
2. BufferedOutputStream

    BufferedOutputStream可以为输出字节流提供缓冲区，作用与BufferedInputStream类似


    ```Java
    /**
     * outputStream 输出流
     * 1024 内部缓冲区大小为1024byte
     */
    BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(outputStream,1024);
    ```

>字节流提供了带缓冲区的，那字符流肯定也提供了BufferedReader和BufferedWriter

3. BufferedReader

    ```java
    BufferedReader bufferedReader = new BufferedReader(reader,1024);
    ```

4. BufferedWriter


```Java
BufferedWriter bufferedWriter = new BufferedWriter(writer,1024);
```

####3、 BIO模型 网络编程

当使用BIO模型进行Socket编程的时候，服务端通常使用while循环中调用accept方法，在没有客户端请求时，accept方法会一直阻塞，直到接收到请求并返回处理的相应，这个过程都是线性的，只有处理完当前的请求之后才会接受处理后面的请求，这样通常会导致通信线程被长时间阻塞,并且在accept后服务端拿到客户端返回的socket时,socket.getInputStream()也会阻塞当前线程直到读取到数据.如果单线程会导致连接到A用户连接到server后不发消息，其他用户则不能进行登录的窘境。

（1）BIO模型处理单个连接

```Java
/**
 * TODO:<p> 普通socket交互-服务端 <p/>
 *
 * @package: net.test1
 * @Author mac
 * @Date 2020/4/2 8:08 下午
 * @Version V1.0
 **/

public class ServerSocketClass implements Runnable{
    private String ip;
    private int port;
    private int socketNum;
    private ServerSocket server=null;
    Log log= LogFactory.get();

    public ServerSocketClass(String ip, int port, int socketNum) {
        this.ip = ip;
        this.port = port;
        this.socketNum = socketNum;
    }
    @Override
    public void run() {
        try {
            server=new ServerSocket(this.port,this.socketNum,InetAddress.getByName("127.0.0.1"));
            while (true){
                Socket socket=server.accept();//阻塞

                BufferedReader reader=new BufferedReader(new InputStreamReader(socket.getInputStream(),"UTF-8"));
                PrintStream stream=new PrintStream(socket.getOutputStream(),true,"UTF8");

                String s=null;
                while ((s=reader.readLine())!=null){//读取到数据
                    log.info("读取到数据 ： {}",s);
                    stream.println(" ： {"+s+"}");
                }
                reader.close();
                stream.close();
                socket.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}



/**
 * TODO:<p> 普通socket交互-客户端 <p/>
 *
 * @package: net.test1
 * @Author mac
 * @Date 2020/4/2 8:06 下午
 * @Version V1.0
 **/

public class ClientSocket implements Runnable{
    private String ip;
    private int port;
    private int timeout;
    private Socket socket;
    private Log log= LogFactory.get();


    public ClientSocket(String ip, int port, int timeout) {
        this.ip = ip;
        this.port = port;
        this.timeout = timeout;
    }

    @Override
    public void run() {

        try {
            socket=new Socket(InetAddress.getByName(this.ip),this.port);
            socket.setSoTimeout(this.timeout);
        } catch (UnknownHostException e) {
            throw new RuntimeException("ip解析失败");
        } catch (SocketException e) {
            throw new RuntimeException("socket链接异常，请检查服务端是否启动，以及IP和端口是否正确");
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            BufferedReader reader=new BufferedReader(new InputStreamReader(System.in,"UTF-8"));
            BufferedReader readerFromServer=new BufferedReader(new InputStreamReader(socket.getInputStream(),"UTF-8"));

            PrintStream stream=new PrintStream(socket.getOutputStream(),true,"UTF-8");
            String s=null;
            while ((s=reader.readLine())!=null){//用户输入
                //发送给服务端
                stream.println(s);//刷新缓冲
                //跳出循环
                break;
            }
            //接受信息
            String ss=null;
            while ((ss=readerFromServer.readLine())!=null){
                log.info("收到服务端返回信息 : {}",ss);
            }

        } catch (IOException e) {
            throw new RuntimeException("获取io流时服务器断线！！");
        }
    }
}
```


（2）BIO模型处理多个连接

在这种模式中我们通常用一个线程去接受请求，然后用一个线程池去处理请求，用这种方式并发管理多个Socket客户端连接，像这样：

```Java

/**
 * TODO:<p> 解决test2中遗留问题 > 单人聊天不行以及群发消息时候给自己也发了<p/>
 *
 * @package: net.test1
 * @Author mac
 * @Date 2020/4/2 8:08 下午
 * @Version V1.0
 **/

public class ServerSocketClass implements Runnable{
    private String ip;
    private int port;
    private int socketNum;
    private ServerSocket server=null;
    private static ConcurrentHashMap<String, Socket> userSocketInfo=new ConcurrentHashMap<>();;
    private Log log= LogFactory.get();

    public ServerSocketClass(String ip, int port, int socketNum) {
        this.ip = ip;
        this.port = port;
        this.socketNum = socketNum;
    }

    @Override
    public void run() {
        try {
            server = new ServerSocket();
            server.bind(new InetSocketAddress(this.ip,this.port),this.socketNum);//服务端支持80个客户端连接。超出报错
            while (true){
                Socket socket=server.accept();//阻塞当前进程，监听端口


                ServerSubThread subThread=new ServerSubThread(socket);
                new Thread(subThread).start();
            }
        } catch (IOException e) {
            log.error("服务器启动失败，可能是端口号："+this.port+"被占用!");
        }finally {

        }
    }


    /**
     * TODO:<p> 不停地从客户端读取信息 <p/>
     *
     * @package: net.test2
     * @Author mac
     * @Date 2020/4/2 8:12 下午
     * @Version V1.0
     **/
    public class ServerSubThread implements Runnable{
        private Socket socket;
        private BufferedReader reader;
        /** key :用户名   value : 用户对应socket*/
        private PrintStream printToCli;
        public ServerSubThread(Socket socket) {
            this.socket = socket;
        }

        public void init(){
            try {
                reader=new BufferedReader(new InputStreamReader(socket.getInputStream(),"UTF-8"));
                printToCli=new PrintStream(socket.getOutputStream(),true,"UTF-8");
            } catch (IOException e) {
                closeRes("读取客户端数据时，客户端关闭，获取失败");
            }
        }

        @Override
        public void run() {
            //初始化
            init();

            String s=null;
            while ((s=getMsg())!=null){
                log.info("读取到数据 > {}",s);

                //用户登录
                if(s.startsWith(ChatRoomProtocol.USER_ROUND) && s.endsWith(ChatRoomProtocol.USER_ROUND)){
                    String user=s.substring(ChatRoomProtocol.USER_ROUND.length(), s.length()-ChatRoomProtocol.USER_ROUND.length());
                    //验证用户登录
                    if(userSocketInfo.containsKey(user)){
                        printToCli.println(ChatRoomProtocol.LOGIN_Failed);
                        log.error(user+" > 用户登录失败");
                    }else {
                        printToCli.println(ChatRoomProtocol.LOGIN_SUCCESS);
                        userSocketInfo.put(user,socket);
                        log.info(user+" > 用户登录成功");
                    }
                }

                //群聊
                if(s.startsWith(ChatRoomProtocol.PUBLICMSG_ROUND) && s.endsWith(ChatRoomProtocol.PUBLICMSG_ROUND)){
                    String[] userAndMsg = s.replaceAll(ChatRoomProtocol.PUBLICMSG_ROUND, "").split(ChatRoomProtocol.SPLIT_SIGN);
                    String sendMsgUser=userAndMsg[0];
                    String msg= userAndMsg.length==1 ? "": userAndMsg[1];
                    for (Map.Entry<String, Socket> entity : userSocketInfo.entrySet()) {
                        try {
                            PrintStream printStream=new PrintStream(entity.getValue().getOutputStream(),true,"UTF-8");
                            printStream.println(sendMsgUser+" 说 ： "+msg);
                        } catch (IOException e) {
                            closeRes("输出数据到客户端时，连接断开，发送失败");
                        }
                    }
                }
                //私聊  格式姓名：msg
                if(s.startsWith(ChatRoomProtocol.PRIVATEMSG_ROUND)&& s.endsWith(ChatRoomProtocol.PRIVATEMSG_ROUND)){
                    String[] userAndMsg = null;
                    if(s.replaceAll(ChatRoomProtocol.PRIVATEMSG_ROUND, "").contains(":")){
                        userAndMsg= s.replaceAll(ChatRoomProtocol.PRIVATEMSG_ROUND, "").split("：");
                    }
                    if(s.replaceAll(ChatRoomProtocol.PRIVATEMSG_ROUND, "").contains("：")){
                        userAndMsg = s.replaceAll(ChatRoomProtocol.PRIVATEMSG_ROUND, "").split("：");
                    }

                    if(userAndMsg.length<2){
                        log.error("格式有误 ！！ ");
                        continue;
                    }
                    String msg= userAndMsg[1];
                    String sendMsgUser=userAndMsg[0].split(ChatRoomProtocol.SPLIT_SIGN)[0];
                    String targerUser=userAndMsg[0].split(ChatRoomProtocol.SPLIT_SIGN)[1];
                    boolean flag=false;
                    for (String name : userSocketInfo.keySet()) {
                        if(name.equals(targerUser)){
                            try {
                                PrintStream printStream=new PrintStream(userSocketInfo.get(name).getOutputStream(),true,"UTF-8");
                                printStream.println(sendMsgUser+" 说 ： "+msg);
                                flag=true;
                            } catch (IOException e) {
                                closeRes("输出数据到客户端时，连接断开，发送失败");
                            }
                            break;
                        }
                    }
                    if(!flag){
                        printToCli.println("没有该用户！！");
                    }
                }

            }

        }
        /*
         * TODO <p> 获取客户端消息</p>
         * @author mac
         * @date 2020/4/2 8:22 下午
         *
         * @param
         * @return java.lang.String
         * @see #
         */
        public String getMsg(){
            String s=null;
            try {
                s=this.reader.readLine();
            }catch (IOException e){
                if(userSocketInfo.containsValue(this.socket)){
                    for (Map.Entry<String, Socket> entry : userSocketInfo.entrySet()) {
                        if(this.socket.equals(entry.getValue())){
                            userSocketInfo.remove(this.socket);
                        }
                    }
                }
                closeRes("读取客户端数据时，客户端关闭，获取失败");
            }
            return s;
        }
        /*
         * TODO <p>关闭资源 </p>
         * @author mac
         * @date 2020/4/3 5:38 下午
         *
         * @param
         * @return void
         * @see #
         */
        public void closeRes(final String msg){
            try {
                if(reader!=null){
                    reader.close();
                }
                if(printToCli!=null){
                    printToCli.close();
                }
                if(socket!=null){
                    socket.close();
                }
            } catch (IOException ex) {
                ex.printStackTrace();
            }
            throw new RuntimeException(msg);
        }
    }

}



/**
 * TODO:<p> 实现聊天室中单人聊天以及群聊天 <p/>
 *
 * @package: net.test1
 * @Author mac
 * @Date 2020/4/2 8:06 下午
 * @Version V1.0
 **/

public class ClientSocket implements Runnable{
    private String ip;
    private int port;
    private Socket socket;
    private PrintStream printStream;
    private BufferedReader readerFromCli;
    private BufferedReader reader;
    /** 每个用户对应一个线程，name为该用户名称*/
    private String name;
    private Log log= LogFactory.get();


    public ClientSocket(String ip, int port) {
        this.ip = ip;
        this.port = port;
    }
    public void init(){
        socket=new Socket();
        try {
            socket.connect(new InetSocketAddress(this.ip,this.port));
//            socket.setSoTimeout(this.timeout);//超时
        } catch (UnknownHostException e) {
            throw new RuntimeException("找不到服务器，请确认服务器是后启动！");
        } catch (SocketException e) {
            throw new RuntimeException("socket链接异常，请检查服务端是否启动，以及IP和端口是否正确");
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            printStream=new PrintStream(socket.getOutputStream(),true,"UTF-8");
            readerFromCli=new BufferedReader(new InputStreamReader(System.in,"UTF-8"));
            this.reader=new BufferedReader(new InputStreamReader(this.socket.getInputStream(),"UTF-8"));
        } catch (IOException e) {
            closeRes("socket链接异常，请检查网络");
        }
    }

    @Override
    public void run() {
        //初始化
        init();

        //登录
        String tip = "";
        while (true){
            String userName = JOptionPane.showInputDialog(tip + "输入用户名：");
            //就把用户输入的用户名发送给服务器
            printStream.println(ChatRoomProtocol.USER_ROUND + userName + ChatRoomProtocol.USER_ROUND);
            //发送后，紧接着获取服务器的响应
            String Login_result = null;

            try {
                Login_result = reader.readLine();
            } catch (IOException e) {
                closeRes("socket链接异常，请检查网络");
            }
            //用户名重复了，返回-1
            if(Login_result.equals(ChatRoomProtocol.LOGIN_Failed)){
                tip = "用户名重复，请重新";
                continue;
            }
            if(Login_result.equals(ChatRoomProtocol.LOGIN_SUCCESS)){
                log.info(userName+" > 登录成功！ ");
                name=userName;
                break;
            }
        }

        //接受服务器信息
        ClientSubThread subThread=new ClientSubThread(this.socket,this.reader);
        new Thread(subThread).start();

        //接受用户发送消息
        String s=null;
        while ((s=readFromCli())!=null){
            if(s.contains(":")||s.contains("：")){
                printStream.println(ChatRoomProtocol.PRIVATEMSG_ROUND+name+ChatRoomProtocol.SPLIT_SIGN+s+ChatRoomProtocol.PRIVATEMSG_ROUND);
            }else{
                printStream.println(ChatRoomProtocol.PUBLICMSG_ROUND+name+ChatRoomProtocol.SPLIT_SIGN+s+ChatRoomProtocol.PUBLICMSG_ROUND);
            }

            continue;
        }
    }
    /*
     * TODO <p>从控制台获取用户输入信息 </p>
     * @author mac
     * @date 2020/4/3 3:18 下午
     *
     * @param
     * @return java.lang.String
     * @see #
     */
    public String readFromCli(){
        String s=null;
        try {
            s=readerFromCli.readLine();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return s;
    }

    /*
     * TODO <p> 从服务端读取信息</p>
     * @author mac
     * @date 2020/4/2 8:48 下午
     *
     * @param
     * @return java.lang.String
     * @see #
     */
    public String readFromServer(){
        String s=null;
        //接受信息
        try {
            s=reader.readLine();
        }catch (IOException e){
            throw new RuntimeException("用户输入信息异常 ！1");
        }
        return s;
    }
    /*
     * TODO <p>关闭资源 </p>
     * @author mac
     * @date 2020/4/3 5:38 下午
     *
     * @param
     * @return void
     * @see #
     */
    public void closeRes(final String msg){
        try {
            if(reader!=null){
                reader.close();
            }
            if(printStream!=null){
                printStream.close();
            }
            if(socket!=null){
                socket.close();
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
        throw new RuntimeException(msg);
    }


    /**
     * TODO:<p> 不停的从服务端读取信息 <p/>
     *
     * @package: net.test2
     * @Author mac
     * @Date 2020/4/2 8:37 下午
     * @Version V1.0
     **/
    public class ClientSubThread implements Runnable {
        private Socket socket;
        private BufferedReader reader;

        public ClientSubThread(Socket socket,BufferedReader reader) {
            this.socket = socket;
            this.reader=reader;
        }

        @Override
        public void run() {

            try {
                String s=null;
                while ((s=reader.readLine())!=null) {
//                String msg=s.substring(ChatRoomProtocol.PUBLICMSG_ROUND.length(), s.length()-ChatRoomProtocol.PUBLICMSG_ROUND.length());
                    log.info("收到服务端返回信息 > {}",s);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
        /*
         * TODO <p> 从服务端读取信息</p>
         * @author mac
         * @date 2020/4/2 8:48 下午
         *
         * @param
         * @return java.lang.String
         * @see #
         */
        public String redMsg(){
            String s=null;
            //接受信息
            try {
                s=reader.readLine();
            }catch (IOException e){
                throw new RuntimeException("用户输入信息异常 ！1");
            }
            return s;
        }


    }

}


public interface ChatRoomProtocol {
    //可以修改为枚举类
    int PROTOCOL_LEN = 2;
    String PUBLICMSG_ROUND = "】⊿"; //公聊消息的前后缀
    String USER_ROUND = "﹩○"; //用户名称的前后缀
    String LOGIN_SUCCESS = "1"; //登录成功的前后缀
    String LOGIN_Failed = "-1"; //后面在客户端发送信息前，要求输入用户名，重复了返回这个标记
    String PRIVATEMSG_ROUND = "﹩＆"; //私聊信息的前后缀
    String SPLIT_SIGN = "？※"; //信息的分割标记
}

```



####4、 Bio模型缺陷
>使用BIO模型进行网络编程的问题在于缺乏弹性伸缩能力，客户端并发访问数量和服务器线程数量是1:1的关系，而且平时由于阻塞(连接阻塞和等待数据阻塞)会有大量的线程处于等待状态，等待输入或者输出数据就绪，造成资源浪费，在面对大量并发的情况下，如果不使用线程池直接new线程的话，就会大致线程膨胀，系统性能下降，有可能导致堆栈的内存溢出，而且频繁的创建销毁线程，更浪费资源

>使用线程池可能是更优一点的方案，但是无法解决阻塞IO的阻塞问题，而且还需要考虑如果线程池的数量设置较小就会拒绝大量的Socket客户端的连接，如果线程池数量设置较大的时候，会导致大量的上下文切换，而且程序要为每个线程的调用栈都分配内存，其默认值大小区间为 64 KB 到 1 MB，浪费虚拟机内存

>不支持单线程并发，上面的案例都是通过多线程来解决的。

####5、 使用场景
BIO模型适用于链接数目固定而且比较少的架构，但是使用这种模型写的代码更直观简单易于理解

###<code><font color=ff00ff >NIO</font></code>

JDK 1.4版本以来，JDK发布了全新的I/O类库，简称NIO，是一种同步非阻塞IO模型。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同， NIO支持``面向缓冲区``的、``基于通道``的IO操作。 NIO将以更加高效的方式进行文件的读写操作。以及使用``同步非阻塞(单线程并发)``网络数据交互。

<font size=4>解决问题</font>

<font color=ffaa00>解决BIO单线程并发问题(accept和读取数据时阻塞问题)。和Redis实现单线程并发一个原理：都是调用了系统的epoll函数。</font>

####1、 NIO概念

    Java NIO系统的核心在于：通道(Channel)和缓冲区(Buffer)。通道表示打开到 IO 设备(例如：文件、套接字)的连接。若需要使用 NIO 系统，需要获取用于连接 IO 设备的通道以及用于容纳数据的缓冲区。然后操作缓冲区，对数据进行处理。

#### 2、 缓冲区

    可以存放特定``基本数据类型``的容器，不同的数据类型拥有不同的缓冲区。所有缓冲区都是buffer抽象类的子类。底层是一个数组。

有如下buffer常用子类

* ByteBuffer
* CharBuffer
* ShortBuffer
* IntBuffer
* LongBuffer
* FloatBuffer
* DoubleBuffer

<font size=4.5><strong>基本属性</strong></font>

``容量 (capacity) ``
 表示 Buffer 最大数据容量（即初始化时的大小），缓冲区容量不能为负，并且创建后不能更改。

``限制 (limit)``
 第一个不应该读取或写入的数据的索引，<font color=ffaa00>即位于 limit 后的数据不可读写。</font>缓冲区的限制不能为负，并且不能大于其容量。

``位置 (position)`` 下一个要读取或写入的数据的索引。缓冲区的位置不能为负，并且不能大于其限制.类似于数组中的指针。

``标记 (mark)与重置 (reset)``
标记是一个索引，通过 Buffer 中的 mark() 方法指定 Buffer 中一个特定的 position，之后可以通过调用reset() 方法恢复到这个 position.一般用来多次读取相同的数据。

![缓冲区属性](/assets/缓冲区属性.jpg)

![buffer常用方法](/assets/buffer常用方法.jpg)

<font size=4>缓冲区数据操作</font>

获取 Buffer 中的数据

    get() ：读取单个字节
    get(byte[] dst)：批量读取多个字节到 dst 中
    get(int index)：读取指定索引位置的字节(不会移动 position)

放入数据到 Buffer 中

    put(byte b)：将给定单个字节写入缓冲区的当前位置
    put(byte[] src)：将 src 中的字节写入缓冲区的当前位置
    put(int index, byte b)：将指定字节写入缓冲区的索引位置(不会移动 position)

```Java
/** 缓冲区 */
//1.分配一个指定大小的缓冲区
ByteBuffer buffer = ByteBuffer.allocate(1024);//非直接缓冲区 ByteBuffer.allocateDirect()//直接缓冲区
//2.将数据放入到字节缓冲区
buffer.put("hello world".getBytes());
log.debug("存入数据时的position ：{}",buffer.position());
//3.切换到读数据模式
buffer.flip();
log.debug("切换到读数据模式的position ：{}",buffer.position());
//4.读取数据
byte[] datas=new byte[buffer.limit()];
buffer.get(datas);
log.debug("读取数据后的的position ：{}",buffer.position());
log.debug("读取到的数据为 : {}", new String(datas,0,datas.length));
//5.重复读，由于position已经指向在hello world中d位置，因此读不到
    //错误示范
byte[] twoDatas=new byte[buffer.limit()];
    //buffer.get(twoDatas);
    //log.debug("再次读取到的数据为 : {}", new String(datas,0,twoDatas.length));
    //正确姿势
buffer.rewind();//指针回到最开始
buffer.get(twoDatas);
log.debug("再次读取到的数据为 : {}", new String(datas,0,twoDatas.length));
//6.清空缓冲区，但是数据任然存在，只是修改的指针的指向
buffer.clear();
log.debug("清空后数据 : {}",(char)buffer.get(0));
//7.mark标记
log.debug("mark前的position为 {}", buffer.position());
buffer.mark();//标记
buffer.get(twoDatas,2,2);//读物两个长度的数据
log.debug("mark后的position为 {}", buffer.position());
buffer.reset();//恢复mark时的位置
log.debug("恢复mark后的position为 {}", buffer.position());
```
>存入数据时的position ：11
切换到读数据模式的position ：0
读取数据后的的position ：11
读取到的数据为 : hello world
再次读取到的数据为 : hello world
清空后数据 : h
mark前的position为 0
mark后的position为 2
恢复mark后的position为 0



<font size=4>直接缓冲区与非直接缓冲区</font>

简单来说，日常程序中使用IO操作``磁盘文件(并不包含网络)``时，都需要将jvm内存数据copy或者被copy到物理机的内存然后润乳磁盘中。这种方式是非直接缓冲区，即尽量在jvm内存中创建缓冲区。另一种方式直接缓冲区则绕过了jvm内存直接在物理机内存中创建缓冲区，省掉了copy步骤。如下图：

![非直接缓冲区](/assets/非直接缓冲区.jpg)

![直接缓冲区](/assets/直接缓冲区.jpg)


#### 3、 通道(初始化时参数文件或目标的对象)

类似于连接两个水池的水管。表示从一段到对端的通道。主要用来传输缓冲区(水池)的数据。

有如下Channel常用实现类

1. FileChannel：用于读取、写入、映射和操作文件的通道。
2. DatagramChannel：通过 UDP 读写网络中的数据通道。
3. SocketChannel：通过 TCP 读写网络中的数据。
4. ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

<strong>1、 获取通道</strong>

获取通道的一种方式是对支持通道的对象调用getChannel() 方法。支持通道的类如下：

* FileInputStream(本地io)
* FileOutputStream(本地io)
* RandomAccessFile(本地io)
* DatagramSocket(网路io)
* Socket(网路io)
* ServerSocket(网路io)

获取通道的其他方式是使用 Files 类的静态方法newByteChannel() 获取字节通道。或者通过通道的静态方法 open() 打开并返回指定通道。

<strong>2、数据传输</strong>


```Java
@Test
public void ChannelDemo() throws IOException {
    /** 通道 --使用直接缓冲区*/
    long startTime=System.currentTimeMillis();
    //1.获取通道
    //读通道
    FileChannel inChannel= (FileChannel) Files.newByteChannel(Paths.get("/Users/mac/Downloads/Java+NIO.pdf"), StandardOpenOption.READ);
    //写通道 -- CREATE（没有文件则创建有则覆盖）CREATE_NEW(没有则创建有则报错）
    FileChannel outChannel= FileChannel.open(Paths.get("/Users/mac/Downloads/new_Java+NIO.pdf"),StandardOpenOption.WRITE,StandardOpenOption.CREATE);
    //FileChannel channel2= new FileInputStream("/Users/mac/Downloads/Java+NIO.pdf").getChannel();//开启时最好将FileInputStream赋值给一个变量，方便关闭
    //2.分配指定大小缓冲区
    ByteBuffer byteBuffer = ByteBuffer.allocateDirect(1024);//直接缓冲区
    //3.将通道中的数据写入缓冲区
    try{
        while (inChannel.read(byteBuffer)!=-1){//读取数据到byteBuffer中
            byteBuffer.flip();//指针回归
            //4.写入数据到输出通道中
            outChannel.write(byteBuffer);
            byteBuffer.clear();//清空缓冲区
        }
    }catch (Exception e){
        log.error(e.getLocalizedMessage());
    }finally {
        if(inChannel!=null){
            inChannel.close();
        }
        if(outChannel!=null){
            outChannel.close();
        }
    }
    log.info("使用直接缓冲区赋值数据耗时 {}",System.currentTimeMillis()-startTime);
}     
```
> 使用直接缓冲区赋值数据耗时 24

```Java
@Test
public void ChannelDemo2() throws IOException {
    /** 通道 --使用非直接缓冲区*/
    long startTime=System.currentTimeMillis();
    //1.获取通道
    //读通道
    FileChannel inChannel= (FileChannel) Files.newByteChannel(Paths.get("/Users/mac/Downloads/Java+NIO.pdf"), StandardOpenOption.READ);
    //写通道 -- CREATE（没有文件则创建有则覆盖）CREATE_NEW(没有则创建有则报错）
    FileChannel outChannel= FileChannel.open(Paths.get("/Users/mac/Downloads/new_Java+NIO.pdf"),StandardOpenOption.WRITE,StandardOpenOption.CREATE);
    //FileChannel channel2= new FileInputStream("/Users/mac/Downloads/Java+NIO.pdf").getChannel();//开启时最好将FileInputStream赋值给一个变量，方便关闭
    //2.分配指定大小缓冲区
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);//非直接缓冲区
    //3.将通道中的数据写入缓冲区
    try{
        while (inChannel.read(byteBuffer)!=-1){//读取数据到byteBuffer中
            byteBuffer.flip();//指针回归
            //4.写入数据到输出通道中
            outChannel.write(byteBuffer);
            byteBuffer.clear();//清空缓冲区
        }
    }catch (Exception e){
        log.error(e.getLocalizedMessage());
    }finally {
        if(inChannel!=null){
            inChannel.close();
        }
        if(outChannel!=null){
            outChannel.close();
        }
    }
    log.info("使用直接缓冲区赋值数据耗时 {}",System.currentTimeMillis()-startTime);
}
```
>使用直接缓冲区赋值数据耗时 39


<strong>3、分散(Scatter)和聚集(Gather)</strong>
```Java
//分散(Scatter)和聚集(Gather)
FileChannel fileChannel=FileChannel.open(Paths.get("src/main/resources/resource.txt"), StandardOpenOption.READ);

//分配两个指定大小的缓冲区
ByteBuffer charBuffer=ByteBuffer.allocate(100);
ByteBuffer charBuffer1=ByteBuffer.allocate(1024);

//分散读取
ByteBuffer[] charBuffers={charBuffer,charBuffer1};
fileChannel.read(charBuffers);

for (ByteBuffer buffer : charBuffers) {
   buffer.flip();
}

System.out.println(new String(charBuffers[0].array(),0,charBuffers[0].limit()));
System.out.println("======================");
System.out.println(new String(charBuffers[1].array(),0,charBuffers[1].limit()));

```

```Java
RandomAccessFile ra=null;
FileChannel readFile=null;
FileChannel writeFile=null;
try {
    //聚集写入
    File file=new File("/Users/mac/Downloads/timg.jpeg");
    int size=(int) file.length();
    readFile=FileChannel.open(Paths.get("/Users/mac/Downloads/timg.jpeg"),StandardOpenOption.READ);//共34k
    log.debug("文件大小为 ： {}", size);
    ByteBuffer buffer1=ByteBuffer.allocate(size/2);
    ByteBuffer buffer2=ByteBuffer.allocate(size/2+1);
    ByteBuffer[] readDatas={buffer1,buffer2};
    readFile.read(readDatas);//分散读取

    //写入
    for (ByteBuffer data : readDatas) {
        data.flip();//指针回归
    }

    ra = new RandomAccessFile(new File("src/main/resources/高圆圆.jpeg"), "rw");
    writeFile=ra.getChannel();
    writeFile.write(readDatas);//将缓冲区数据合并写入到new文件中
}catch (IOException e){
    log.error(e.getLocalizedMessage());
}finally {
    if(ra!=null){
        ra.close();
    }
    if(writeFile!=null){
        writeFile.close();
    }
}
```



<strong>4、 transferFrom()和transferTo()-- 上诉图片拷贝可升级为此种方式</strong>

```Java
/*
 * TODO <p> 通道之间的数据传输(直接缓冲区)</p>
 * @author mac
 * @date 2020/4/5 11:27 下午
 * @param
 * @return void
 * @see #
 */
@Test
public void transferFromAndTo() throws IOException {
    FileChannel inChannel=null;
    FileChannel outChannel=null;

    try {
        inChannel = FileChannel.open(Paths.get("/Users/mac/Downloads/timg.jpeg"), StandardOpenOption.READ);
        outChannel=FileChannel.open(Paths.get("src/main/resources/高圆圆.png"), StandardOpenOption.WRITE,StandardOpenOption.CREATE);
        //
        inChannel.transferTo(0, inChannel.size(), outChannel);
        //二选一
//            outChannel.transferFrom(inChannel, 0, inChannel.size());
    }catch (IOException e){
        log.error(e.getLocalizedMessage());
    }finally {
        if (inChannel!=null){
            inChannel.close();
        }
        if(outChannel!=null){
            outChannel.close();
        }
    }

}
```

<strong>5、Charset(字符集)</strong>

```Java
//字符集
@Test
public void test6(){
    Charset cs1=Charset.forName("GBK");

    //获取编码器
    CharsetEncoder ce=cs1.newEncoder();

    //获取解码器
    CharsetDecoder cd=cs1.newDecoder();

    CharBuffer cBuf=CharBuffer.allocate(1024);
    cBuf.put("啦啦哈哈吧吧");
    cBuf.flip();

    //编码
    ByteBuffer bBuf=null;
    try {
        bBuf = ce.encode(cBuf);//将cbuf中数据按照ce编码器编码
    } catch (CharacterCodingException e) {
        e.printStackTrace();
    }

    for(int i=0;i<12;i++){
        System.out.println(bBuf.get());//-64-78-64-78-71-2-7-2-80-55-80-55
    }

    //解码
    bBuf.flip();
    CharBuffer cBuf2=null;
    try {
        cBuf2 = cd.decode(bBuf);
    } catch (CharacterCodingException e) {
        e.printStackTrace();
    }
    System.out.println(cBuf2.toString());//啦啦哈哈吧吧
}
```


![filechannrel常用方法](/assets/filechannrel常用方法.jpg)


#### 4、非阻塞网络通信

    传统BIO单线程阻塞由两个原因导致。一是：accept()阻塞线程等待客户端连接。二是：客户端连接后调用socket的流式读取数据阻塞等待客户端发送数据。因此解决这两个阻塞就解决了bio的缺陷问题。下面将围绕这两个阻塞点一个一个解决。

<font size=4><strong>1、选择器(Selector)</strong></font>

    选择器（Selector） 是 SelectableChannle 对象的``多路复用器``，Selector 可以同时监控多个 SelectableChannel 的 IO 状况，也就是说，利用 Selector可使一个单独的线程管理多个 Channel。Selector 是非阻塞 IO 的核心。简单原理就是将需要监控的(阻塞)socket(也可以说是通道)和(阻塞)流加入到一个数组中，然后不停的轮训是否客户端连接或者是否发送数据。

>Nio网络通信三个核心:① 通道：负责连接。② 缓冲区：负责数据操作。③ 选择器：多路复用器，用于监控通道的io状态。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="/assets/selector常用方法.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">选择器常用方法</div>
</center>

<font size=4><strong>（1）SelectionKey</strong></font>

当调用 register(Selector sel, int ops) 将通道注册选择器时，选择器对通道的监听事件，需要通过第二个参数 ops 指定。
可以监听的事件类型（用 可使用 SelectionKey 的四个常量 表示）：
 读 : SelectionKey.OP_READ （1）
 写 : SelectionKey.OP_WRITE （4）
 连接 : SelectionKey.OP_CONNECT （8）
 接收 : SelectionKey.OP_ACCEPT （16）

>若注册时不止监听一个事件，则可以使用“位或”操作符连接。

SelectionKey：表示 SelectableChannel 和 Selector 之间的注册关系。简单来说就是放入``选择器``中的通道或者流，你需要选择器帮你监控这两兄弟哪种状态。比如通道就有连接，读写等状态。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="/assets/selectionkey.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">selectionkey常用方法</div>
</center>

```Java
/**
 * TODO:<p> nio非阻塞式网络通信 <p/>
 *
 * @package: nio
 * @Author mac
 * @Date 2020/4/6 10:44 上午
 * @Version V1.0
 **/
public class NetSocketDemo {
    public class Server{
        public void server() throws IOException {
            //1.获取通道
            ServerSocketChannel serverChannel=ServerSocketChannel.open();

            //2.切换非阻塞式模式
            serverChannel.configureBlocking(false);

            //3.绑定连接
            serverChannel.bind(new InetSocketAddress("127.0.0.1",5555));

            //4.获取选择器
            Selector selector = Selector.open();

            //5.将通道加入到选择器，并设置监听类型
            serverChannel.register(selector, SelectionKey.OP_ACCEPT);

            //6.轮训监听
            while (selector.select()>0){
                //7.获取当前选择器中所有注册的“选择键（已就绪的监听事件）”
                Iterator<SelectionKey> it = selector.selectedKeys().iterator();
                while (it.hasNext()){
                    //8.获取准备就绪的事件
                    SelectionKey key = it.next();
                    //9.判断是否接受准备就绪
                    if(key.isAcceptable()){
                        //10.如果接受准备就绪，则此时再获取客户端连接
                        SocketChannel socketChannel = serverChannel.accept();
                        //11.切换非阻塞模式
                        socketChannel.configureBlocking(false);
                        //12.将该已连接后获取的socket消息互传通道注册到选择器上。监控其读写状态
                        socketChannel.register(selector, SelectionKey.OP_READ);
                    }else if(key.isReadable()){
                        //13.获取读就绪状态的通道，即此时用户输入数据，
                        SocketChannel channel = (SocketChannel) key.channel();
                        //14.读取数据
                        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                        int len=0;
                        while ((len=channel.read(byteBuffer))>0){
                            byteBuffer.flip();//切换到读数据模式
                            System.out.println(new String(byteBuffer.array(),0,len));
                            byteBuffer.clear();
                        }
                    }
                    //15.如果不是接受和读状态则取消键
                    it.remove();
                }
            }


        }
    }


    public class Client{
        public void client() throws IOException{
            //1.获取通道
            SocketChannel sChannel=SocketChannel.open(new InetSocketAddress("127.0.0.1", 5555));
            //2.切换非阻塞模式
            sChannel.configureBlocking(false);
            //3.分配指定大小的缓冲区
            ByteBuffer buf=ByteBuffer.allocate(1024);
            //4.发送数据给服务端
            Scanner scan=new Scanner(System.in);
            while(scan.hasNext()){
                String str=scan.next();
                buf.put((new Date().toString()+"\n"+str).getBytes());
                buf.flip();
                sChannel.write(buf);
                buf.clear();
            }
            //5.关闭通道
            sChannel.close();
        }
    }

    public static void main(String[] args) {
        NetSocketDemo netSocketDemo=new NetSocketDemo();
        NetSocketDemo.Server server=netSocketDemo.new Server();
        try {
            server.server();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
###test
public class clientTest {
    public static void main(String[] args) {
        NetSocketDemo netSocketDemo=new NetSocketDemo();
        NetSocketDemo.Client client=netSocketDemo.new Client();
        try {
            client.client();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
> ![nio练习](/assets/nio练习.jpg)

<font size=4><strong>2、DatagramChannel</strong></font>

    Java NIO中的DatagramChannel是一个能收发UDP包的通道。

```Java
public class TestNonBlockNIO2 {
    @Test
    public void send() throws IOException{
        DatagramChannel dc=DatagramChannel.open();
        dc.configureBlocking(false);
        ByteBuffer buf=ByteBuffer.allocate(1024);
        Scanner scan=new Scanner(System.in);
        while(scan.hasNext()){
            String str=scan.next();
            buf.put((new Date().toString()+"\n"+str).getBytes());
            buf.flip();
            dc.send(buf, new InetSocketAddress("127.0.0.1", 9898));
            buf.clear();
        }
        dc.close();
    }

    @Test
    public void receive() throws IOException{
        DatagramChannel dc=DatagramChannel.open();
        dc.configureBlocking(false);
        dc.bind(new InetSocketAddress(9898));
        Selector selector=Selector.open();
        dc.register(selector, SelectionKey.OP_READ);
        while(selector.select()>0){
            Iterator<SelectionKey> it=selector.selectedKeys().iterator();
            while(it.hasNext()){
                SelectionKey sk=it.next();

                if(sk.isReadable()){
                    ByteBuffer buf=ByteBuffer.allocate(1024);
                    dc.receive(buf)
;
                    buf.flip();
                    System.out.println(new String(buf.array(),0,buf.limit()));
                    buf.clear();
                }
            }
            it.remove();
        }
    }
}
```    
<font size=4><strong>3、管道 (Pipe)</strong></font>

    Java NIO 管道是2个线程之间的单向数据连接。Pipe有一个source通道和一个sink通道。数据会被写到sink通道，从source通道读取。

```Java
public class TestPipe {
    @Test
    public void test1()throws IOException{
        //1.获取管道
        Pipe pipe=Pipe.open();
        //2.将缓冲区中的数据写入管道
        ByteBuffer buf=ByteBuffer.allocate(1024);
        Pipe.SinkChannel sinkChannel=pipe.sink();
        buf.put("通过单向管道发送数据".getBytes());
        buf.flip();
        sinkChannel.write(buf);

        //3.读取缓冲区中的数据
        Pipe.SourceChannel sourceChannel=pipe.source();
        buf.flip();
        int len=sourceChannel.read(buf);
        System.out.println(new String(buf.array(),0,len));

        sourceChannel.close();
        sinkChannel.close();
    }
}
```    

### 工具类

#### 1、Path 与 Paths

（1） java.nio.file.Path 接口代表一个平台无关的平台路径，描述了目录结构中文件的位置。

（2）Paths 提供的 get() 方法用来获取 Path 对象：Path get(String first, String … more) : 用于将多个字符串串连成路径。

Path 常用方法：

    boolean endsWith(String path) : 判断是否以 path 路径结束
    boolean startsWith(String path) : 判断是否以 path 路径开始
    boolean isAbsolute() : 判断是否是绝对路径
    Path getFileName() : 返回与调用 Path 对象关联的文件名
    Path getName(int idx) : 返回的指定索引位置 idx 的路径名称
    int getNameCount() : 返回Path 根目录后面元素的数量
    Path getParent() ：返回Path对象包含整个路径，不包含Path 对象指定的文件路径
    Path getRoot() ：返回调用 Path 对象的根路径
    Path resolve(Path p) :将相对路径解析为绝对路径
    Path toAbsolutePath() : 作为绝对路径返回调用 Path 对象
    String toString() ： 返回调用 Path 对象的字符串表示形式

#### 2、Files 类
java.nio.file.Files 用于操作文件或目录的工具类。

Files常用方法：

    Path copy(Path src, Path dest, CopyOption … how) : 文件的复制
    Path createDirectory(Path path, FileAttribute< ? > … attr) : 创建一个目录
    Path createFile(Path path, FileAttribute< ? > … arr) : 创建一个文件
    void delete(Path path) : 删除一个文件
    Path move(Path src, Path dest, CopyOption…how) : 将 src 移动到 dest 位置
    long size(Path path) : 返回 path 指定文件的大小
    boolean exists(Path path, LinkOption … opts) : 判断文件是否存在
    boolean isDirectory(Path path, LinkOption … opts) : 判断是否是目录
    boolean isExecutable(Path path) : 判断是否是可执行文件
    boolean isHidden(Path path) : 判断是否是隐藏文件
    boolean isReadable(Path path) : 判断文件是否可读
    boolean isWritable(Path path) : 判断文件是否可写
    boolean notExists(Path path, LinkOption … opts) : 判断文件是否不存在
    public static < A extends BasicFileAttributes> A readAttributes(Path path,Class< A > type,LinkOption…options) : 获取与 path 指定的文件相关联的属性。
    SeekableByteChannel newByteChannel(Path path, OpenOption…how) : 获取与指定文件的连接，how 指定打开方式。
    DirectoryStream newDirectoryStream(Path path) : 打开 path 指定的目录
    InputStream newInputStream(Path path, OpenOption…how):获取 InputStream 对象
    OutputStream newOutputStream(Path path, OpenOption…how) : 获取 OutputStream 对象


### Bio和Nio区别

    BIO面向流(单向，同步阻塞)，Nio面向缓冲区(通道双向，同步非阻塞)
