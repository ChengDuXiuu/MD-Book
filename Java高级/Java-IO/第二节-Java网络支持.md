Java为网络编程提供了支持包java.net,该包下提供了InetAddress、ULRDecoder、URLEncoder、URL和URLConnection等等很多工具类，这些类可以让我们通过编程的形式访问Web服务器或者其他的服务器。

###InetAddress类及其常用方法

Internet 上的主机有两种方式表示地址，分别为域名和 IP 地址。java.net 包中的 InetAddress 类对象包含一个 Internet 主机地址的域名和 IP 地址。InetAddress 类提供了操作 IP 地址的各种方法。该类本身没有构造方法，而是通过调用相关静态方法，比如getByName()方法获取实例。InetAddress 类中的常用方法如下表 所示。
![失效](/网络/资料/inetaddress类.jpg)

```java
//获取指定
InetAddress address=InetAddress.getByName("www.qq.com");
System.out.println(address.getHostAddress());
System.out.println(address.getHostName());
//获取本地
InetAddress ia3=InetAddress.getLocalHost();
System.out.println("主机名："+ia3.getHostName());
System.out.println("本地ip地址："+ia3.getHostAddress());
```

###ULRDecoder和URLEncoder
ULRDecoder和URLEncoder的作用主要是实现：普通字符串和application/x-www-form-urlencoded字符串相互转换的功能。application/x-www-form-urlencoded： 是一种应用在网页提交数据的过程中，被提交的数据的编码格式。
>这个编码，我们并不陌生，比如在百度上搜索一下，网址中出现的“乱码”就是application/x-www-form-urlencoded格式的字符。它的基本格式：%XX%XX XX为十六进制的数字。
例如：https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=02003390_42_hao_pg&wd=%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF

为什么会有这种转换呢？

网页的 URL 规定只能包含合法的字符，这可以分成两类。

    URL元字符：分号（;），逗号（’,’），斜杠（/），问号（?），冒号（:），at（@），&，等号（=），加号（+），美元符号（$），井号（#）

    语义字符：a-z，A-Z，0-9，连词号（-），下划线（_），点（.），感叹号（!），波浪线（~），星号（*），单引号（‘），圆括号（()）

除了以上字符，其他字符出现在 URL 之中都必须转义，规则是根据操作系统的默认编码，将每个字节转为百分号（%）加上两个大写的十六进制字母。

>比如，UTF-8 的操作系统上，http://www.example.com/q=春节 这个 URL 之中，汉字“春节”不是 URL 的合法字符，所以被浏览器自动转成http://www.example.com/q=%E6%98%A5%E8%8A%82。
其中，“春”转成了%E6%98%A5，“节”转成了“%E8%8A%82”。这是因为“春”和”节“的 UTF-8 编码分别是E6 98 A5和E8 8A 82，将每个字节前面加上百分号，就构成了 application/x-www-form-urlencoded格式字符串。

###URL类和URLConnection类

在 java.net 包中包含专门用来处理 URL 的 URL类，可以获得 URL 的相关信息，例如 URL 的协议名和主机名等。下面分别对它的构造方法和常用方法进行介绍。

![失效](/网络/资料/url.jpg)


![失效](/网络/资料/url方法.jpg)


###URLConnection 类

完成了 URL 的定义，接下来就可以获得 URL 的通信连接。在 java.net 包中，定义了专门的 URLConnection 类来表示与 URL 建立的通信连接，URLConnection 类的对象使用 URL 类的 openConnection() 方法获得。

>注意：我们在应用程序和URL链接的时候，使用URLConnection类，当我们HTTP和URL链接的时候，使用URLConnection类的子类HttpURLConnection。

![失效](/网络/资料/urlconnection.jpg)

```Java
URL url=new URL("https://www.baidu.com/");
HttpURLConnection connection=(HttpURLConnection)url.openConnection();
InputStream inputStream = connection.getInputStream();
byte[] datas=new byte[8000];
int read=0;
int temp=0;
while ((temp= inputStream.read()) !=-1){
    datas[read++]=(byte)temp;
}
System.out.println(new String(datas,0,datas.length));
```

###案例
使用Java多线程从网络中下载文件

```Java
package net;

import java.io.IOException;
import java.io.InputStream;
import java.io.RandomAccessFile;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;


/**
 * TODO:<p> 多线程下载文件get请求 <p/>
 *
 * @package: net
 * @Author mac
 * @Date 2020/3/29 7:56 下午
 * @Version V1.0
 **/
public class DownUtil {
    private String path;//下载路径
    private String targetFile;//保存路径
    private int threadNum;//线程数目
    private DownThread[] threads;
    private int fileSize;

    public DownUtil(String path,String targetFile,int threadNum){
        this.path=path;
        this.targetFile=targetFile;
        this.threadNum=threadNum;
        threads=new DownThread[threadNum];
    }

    private class DownThread extends Thread{

        //当前线程下载的位置
        private int startPos;
        //当前线程负责下载的大小
        private int currentPartSize;
        //当前线程需要下载的文件块
        private RandomAccessFile currentPart;
        //定义当前线程已经下载的字节数
        public int length;
        //构造函数初始化
        public DownThread(int startPos,int currentPartSize,RandomAccessFile currentPart){
            this.startPos = startPos;
            this.currentPartSize = currentPartSize;
            this.currentPart = currentPart;
        }
        @Override
        public void run() {
            try {
                HttpURLConnection conn = getConn(path,5000);
                //建立实际的链接，可以省略，下面的getInputStream()方法会判断，没链接，自动调用。
                conn.connect();
                InputStream inStream = conn.getInputStream();
                //跳过startPos，表示该线程只下载自己下载的那部分
                inStream.skip(this.startPos);
                byte[] buffer = new byte[1024];
                int hasRead = 0;
                while(length < currentPartSize && (hasRead=inStream.read(buffer))!=-1){
                    currentPart.write(buffer,0,hasRead);
                    //累计线程下载的大小
                    length += hasRead;
                }
                currentPart.close();
                inStream.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }

    public void download() throws Exception {
        HttpURLConnection connection = getConn(path,5000);
        //获取到文件大小
        fileSize=connection.getContentLength();
        connection.disconnect();
        int currentPartSize = fileSize/threadNum + 1;
        RandomAccessFile file = new RandomAccessFile(targetFile,"rw");
        //设置本地文件大小
        file.setLength(fileSize);
        file.close();
        for(int i=0;i<threadNum;i++){
            //计算每个线程下载的开始位置
            int startPos = i * currentPartSize;
            //每个线程使用一个Raf进行下载
            RandomAccessFile currentPart = new RandomAccessFile(targetFile,"rw");
            //定位该线程的下载位置
            currentPart.seek(startPos);
             //创建下载线程
            threads[i] = new DownThread(startPos,currentPartSize,currentPart);
            //启动下载线程
            threads[i].start();
        }
    }

    //获取下载完成的百分百
    public double  getCompleteRate(){
        //统计多个线程已经下载的总大小
        int sumSize = 0;
        for(int i=0;i<threadNum;i++){
            sumSize += threads[i].length;
        }
        //返回已下载的百分比
        return sumSize * 1.0 / fileSize;
    }


    public HttpURLConnection getConn(String path,int time) throws Exception {
        URL url = new URL(path);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setConnectTimeout(time);
        conn.setRequestMethod("GET");conn.setRequestProperty("Accept","*/*");
        conn.setRequestProperty("Accept-Language","zh-CN");
        conn.setRequestProperty("Charset","UTF-8");
        return conn;
    }

}

### test
package net;

import java.io.IOException;

/**
 * TODO:<p>  <p/>
 *
 * @package: net
 * @Author mac
 * @Date 2020/3/29 8:17 下午
 * @Version V1.0
 **/
public class DownUtilTest {
    public static void main(String[] args) throws Exception {
        DownUtil downUtil=new DownUtil("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1585497746973&di=9907181adda74f3499c9703c9f33ee8e&imgtype=0&src=http%3A%2F%2Fb.hiphotos.baidu.com%2Fzhidao%2Fpic%2Fitem%2F5bafa40f4bfbfbed122234427bf0f736afc31f97.jpg", "/Volumes/mac资料/downFile1.jpg", 5);
        downUtil.download();

        new Thread(new Runnable(){

            @Override
            public void run() {
                while (downUtil.getCompleteRate() <= 1){
                    System.out.println("已完成：" + downUtil.getCompleteRate());
                    try {
                        Thread.sleep(50);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }
}

```

```Java
post 请求发送数据
public class SendPostDemo {
    public static void main(String[] args) throws Exception{
        String urlPath = new String("http://localhost:8080/Test1/HelloWorld");
        //String urlPath = new String("http://localhost:8080/Test1/HelloWorld?name=丁
        丁".getBytes("UTF-8"));
        String param="name="+URLEncoder.encode("丁丁","UTF-8");
        //建立连接
        URL url=new URL(urlPath);
        HttpURLConnection httpConn=(HttpURLConnection)url.openConnection();
        //设置参数
        httpConn.setDoOutput(true); //需要输出
        httpConn.setDoInput(true); //需要输入
        httpConn.setUseCaches(false); //不允许缓存
        httpConn.setRequestMethod("POST"); //设置POST方式连接
        //设置请求属性
        httpConn.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        httpConn.setRequestProperty("Connection", "Keep-Alive");// 维持长连接
        httpConn.setRequestProperty("Charset", "UTF-8");
        //连接,也可以不用明文connect，使用下面的httpConn.getOutputStream()会自动connect
        httpConn.connect();
        //建立输入流，向指向的URL传入参数
        DataOutputStream dos=new DataOutputStream(httpConn.getOutputStream());
        dos.writeBytes(param);
        dos.flush();
        dos.close();
        //获得响应状态
        int resultCode=httpConn.getResponseCode();
        if(HttpURLConnection.HTTP_OK==resultCode){
            StringBuffer sb=new StringBuffer();
            String readLine=new String();
            BufferedReader responseReader=new BufferedReader(new
                    InputStreamReader(httpConn.getInputStream(),"UTF-8"));
            while((readLine=responseReader.readLine())!=null){
                sb.append(readLine).append("\n");
            } r
            esponseReader.close();
            System.out.println(sb.toString());
        }
    }
}
 JAVA使用HttpURLConnection发送POST数据是依靠OutputStream流的形式发送
具体编码过程中，参数是以字符串“name=XXX”这种形式发送
```

###总结:

>a: HttpURLConnection的connect()函数，实际上只是建立了一个与服务器的tcp连接，并没有实际发送http请求。无论是post还是get，http请求实际上直到HttpURLConnection的getInputStream()这个函数里面才正式发送出去。

>b:在用POST方式发送URL请求时，URL请求参数的设定顺序是重中之重，对connection对象的一切配置（那一堆set函数）都必须要在connect()函数执行之前完成。而对outputStream的写操作，又必须要在inputStream的读操作之前。这些顺序实际上是由http请求的格式决定的。如果inputStream读操作在outputStream的写操作之前，会抛出例外：java.net.ProtocolException: Cannot write output after reading input.......

>c:http请求实际上由两部分组成，一个是http头，所有关于此次http请求的配置都在http头里面定义，一个是正文content。connect()函数会根据HttpURLConnection对象的配置值生成http头部信息，因此在调用connect函数之前，就必须把所有的配置准备好。

>d: 在http头后面紧跟着的是http请求的正文，正文的内容是通过outputStream流写入的，实际上outputStrea不是一个网络流，充其量是个字符串流，往里面写入的东西不会立即发送到网络，而是存在于内存缓冲区中，待outputStream流关闭时(关闭的时候调用了flush方法)，根据输入的内容生成http正文。至此，http请求的东西已经全部准备就绪。在getInputStream()函数调用的时候，就会把准备好的http请求正式发送到服务器了，然后返回一个输入流，用于读取服务器对于此次http请求的返回信息。由于http 请求在getInputStream的时候已经发送出去了（包括http头和正文），因此在getInputStream()函数之后对connection对象进行设置（对http头的信息进行修改）或者写入outputStream（对正文进行修改）都是没有意义的了，执行这些操作会导致异常的发生
