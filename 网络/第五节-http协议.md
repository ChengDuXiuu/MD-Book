#### 概念
HTTP是HyperText Transfer Protocol的缩写，翻译过来就是超文本传输协议，它是一种用于分布式、协作式和web应用系统的应用层协议。

#### 为什么
既然前面讲了TCP/UDP的网络通信协议，而且他们的使用是非常广泛的，那为啥有多出个http协议来呢？

UDP协议具有不可靠性和不安全性，显然这很难满足web应用的需要。而TCP协议是基于连接和三次握手的，虽然具有可靠性，但任然具有一定的缺陷。

试想一下，B/S架构的网站，十万人同时在线也是很平常的事儿。如果十万个客户端和服务器一直保持连接状态，那服务器那端有这么多带宽来分配吗？

<font color=ffaa00>这就衍生出了http协议。基于TCP的可靠性连接。</font>

>通俗点说，就是在请求之后，服务器端立即关闭连接、释放资源。这样既保证了资源可用，也吸取了TCP的可靠性的优点。


###HTTP工作原理

HTTP协议定义Web客户端如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。
HTTP协议采用了请求/响应模型。客户端向服务器发送一个请求报文，请求报文包含请求行、请求头部、空行和请求数据。服务器收到客户端的请求会响应，响应的内容包括状态行、响应头部、空行和响应数据。

以下是 HTTP 请求/响应的步骤：
1. 浏览器向 DNS 服务器请求解析该 URL 中的域名所对应的 IP 地址，客户端连接到Web服务器
一个HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认为80）建立一个TCP套接字连接。
2. 发送HTTP请求
通过TCP套接字，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。
3. 服务器接收请求并返回HTTP响应
Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读
取。一个响应由状态行、响应头部、空行和响应数据4部分组成。
4. 释放连接TCP连接
若connection 模式为close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放
TCP连接;
<font color=ffaa00>若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收
请求;</font>
5. 客户端浏览器解析HTML内容
客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知HTML文档的若干特殊信息和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

HTTP请求格式
![失效](/网络/资料/http请求格式.jpg)

HTTP请求方法

HTTP/1.1协议中共定义了八种方法（也叫“动作”）来以不同方式操作指定的资源：
GET向指定的资源发出“显示”请求。

HEAD

    类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头。
POST

    向指定资源提交数据，请求服务器进行处理（例如提交表单或者上传文件）。数据被包含在请求数据中。这个请求可能会创建新的资源或修改现有资源，或二者皆有。
PUT

    向指定资源位置上传其最新内容。
DELETE

    请求服务器删除Request-URI所标识的资源。
TRACE

    回显服务器收到的请求，主要用于测试或诊断。
OPTIONS

    这个方法可使服务器传回该资源所支持的所有HTTP请求方法。用'*'来代替资源名称，向Web服务器发送OPTIONS请求，可以测试服务器功能是否正常运
作。

CONNECT

    HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。通常用于SSL加密服务器的链接（经由非加密的HTTP代理服务器）。

###注意事项：

1. 方法名称是区分大小写的。

2. HTTP服务器至少应该实现GET和HEAD方法，其他方法都是可选的。

### URL（Uniform Resource Locator）

超文本传输协议（HTTP）的统一资源定位符将从因特网获取信息的基本元素包括在一个简单的地址中。

URL 的具体语法格式如下所示

>协议://用户名:密码@子域名.域名.顶级域名:端口号/目录/文件名.文件后缀?参数=值
&参数2=值2....#标志

下面是一些简单的 URL 示例:
http://www.sun.com/ 协议名：//主机名
http://localhost:8080/Test/admin/login.jsp 协议名://机器名：端口号/文件名


头部：
1、Accept，浏览器端能够处理的信息内容类型。
Accept: 文件类型使用的MIME值，*/* 代表任意类型MIME (Multipurpose Internet Mail Extensions) 是描述消息内容类型的因特网标准。不用记，用哪个查那个，百度：MIME 参考手册

注意：
docx,application/vnd.openxmlformats-officedocument.wordprocessingml.document
xlsx,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
pptx,application/vnd.openxmlformats-officedocument.presentationml.presentation
ie会把 jpg、jpeg翻译成image/pjpeg，png翻译成image/x-png
q是权重系数，范围 0 =< q <= 1，q 值越大，请求越倾向于获得其“;”之前的类型表示
的内容，
若没有指定 q 值，则默认为1，若被赋值为0，则用于提醒服务器哪些是浏览器不接受的内
容类型。

2、Accept-Encoding， 浏览器能够处理的的压缩编码。
通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate），（注意：这不
是指字符编码）。
例如： Accept-Encoding:gzip, deflate, br


3、Accept-Language， 浏览器当前设置的语言。
语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等；


4、Accept-Charset：浏览器能够显示的字符集

5、Connection：浏览器与服务器的连接类型
例如：Connection: keep-alive 当一个网页打开完成后，客户端和服务器之间用于传输
HTTP数据的TCP连接不会关闭，
如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接。
例如： Connection: close 代表一个Request完成后，客户端和服务器之间用于传输HTTP
数据的TCP连接会关闭。
当客户端再次发送Request，需要重新建立TCP连接。


6、Host，发送请求的页面的域名。


7、Referer，发送请求的页面的URI。当浏览器向web服务器发送请求的时候，一般会带上
Referer，
告诉服务器我是从哪个页面链接过来的，服务器借此可以获得一些信息用于处理。

8、User-Agent，浏览器的用户代理字符串。告诉HTTP服务器， 客户端使用的操作系统和
浏览器的名称和版本。

9、Cookie，用来存储一些用户信息以便让服务器辨别用户身份的。

10、Cache-Control，指明当前资源的有效期，控制浏览器是否直接从浏览器缓存取数据，
还是重新发请求到服务器获取数据。
常看到的：max-age=0，也就是这些资源在浏览器缓存中，保存的时间，值的单位是秒。
再看看HTTP响应消息（服务器返回给浏览器的）：
![失效](/网络/资料/http返回数据.jpg)

HTTP状态码

所有HTTP响应的第一行都是状态行，依次是当前HTTP版本号，3位数字组成的状态代码，以及描述状态的短语，彼此由空格分隔。

状态代码的第一个数字代表当前响应的类型：

1. 1xx消息——请求已被服务器接收，继续处理

2. 2xx成功——请求已成功被服务器接收、理解、并接受

3. 3xx重定向——需要后续操作才能完成这一请求

4. 4xx请求错误——请求含有词法错误或者无法被执行

5. 5xx服务器错误——服务器在处理某个正确请求时发生错误

响应头(消息头)包含:

1、Location：这个头配合302状态吗，用于告诉客户端找谁

2、Server：服务器通过这个头，告诉浏览器服务器的类型

3、Content-Encoding：告诉浏览器，服务器的数据压缩格式

4、Content-Length：告诉浏览器，回送数据的长度

5、Content-Type：告诉浏览器，回送数据的类型

6、Last-Modified：告诉浏览器当前资源缓存时间

7、Refresh：告诉浏览器，隔多长时间刷新

8、Content-Disposition：告诉浏览器以下载的方式打开数据。

>例如： context.Response.AddHeader("ContentDisposition","attachment:filename=aa.jpg");
context.Response.WriteFile("aa.jpg");

9、Transfer-Encoding：告诉浏览器，传送数据的编码格式

10、ETag：缓存相关的头（可以做到实时更新）

11、Expries：告诉浏览器回送的资源缓存多长时间。如果是-1或者0，表示不缓存

12、Cache-Control：控制浏览器不要缓存数据 no-cache

13、Pragma：控制浏览器不要缓存数据 no-cache

14、Connection：响应完成后，是否断开连接。 close/Keep-Alive

15、Date：告诉浏览器，服务器响应时间16、状态行： 例如： HTTP/1.1 200 OK （协议的版本号是1.1 响应状态码为200响应结果为 OK）

17、实体内容（实体头）：响应包含浏览器能够解析的静态内容，例如：html，纯文本，图
片等等信息
