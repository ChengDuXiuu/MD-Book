利用 TCP 协议进行通信的两个应用程序是有主次之分的，一个是服务器程序，一个是客户端程序，两者的功能和编写方法不太一样，其中 ServerSocket 类表示 Socket 服务器端，Socket 类表示 Socket 客户端。

####基于TCP的套接字编程实现流程：

1. 服务器端流程：
（1）创建套接字（ServerSocket）
（2）将套接字绑定到一个本地地址和端口上（bind）
（3）将套接字设定为监听模式，准备接受客户端请求（listen）
（4）阻塞等待客户端请求到来。当请求到来后，接受连接请求，返回一个新的对应于此客户端连接的套接字sockClient（accept）
（5）用返回的套接字sockClient和客户端进行通信（send/recv）；
（6）返回，等待另一个客户端请求（accept）
（7）关闭套接字（close）

2. 客户端流程：
（1） 创建套接字（socket）
（2） 向服务器发出连接请求（connect）
（3） 和服务器进行通信（send/recv）
（4） 关闭套接字（close）

![tcp套接字流程](../资料/tcp套接字流程.jpg)

###ServerSocket 类

>用于在服务器上开一个端口，被动地等待数据（使用 accept() 方法）并建立连接进行数据交互。

>服务器套接字一次可以与一个套接字连接，如果多台客户端同时提出连接请求，请求连接的客户端会被存入一个队列中，然后从中取出一个套接字与服务器新建的套接字连接起来。若请求连接大于最大容纳数，则多出的连接请求被拒绝；默认的队列大小是 50。即一个服务端ServerSocket对象同时可以与默认50个客户端Socket对象建立连接


ServerSocket 的构造方法

    ServerSocket()：无参构造方法。
    ServerSocket(int port)：创建绑定到特定端口的服务器套接字。
    ServerSocket(int port,int backlog)：使用指定的 backlog 创建服务器套接字并将其绑定到指定的本地端口。(这里的backlog指的是上面提到的队列大小，可以指定连接数)
    ServerSocket(int port,int backlog,InetAddress bindAddr)：使用指定的端口、监听 backlog和要绑定到本地的 IP 地址创建服务器。

ServerSocket 的常用方法

    Server accept()：监听并接收到此套接字的连接。

    void bind(SocketAddress endpoint)：将 ServerSocket 绑定到指定地址（IP 地址和端口号）。

    void close()：关闭此套接字。

    InetAddress getInetAddress()：返回此服务器套接字的本地地址。

    int getLocalPort()：返回此套接字监听的端口。

    SocketAddress getLocalSocketAddress()：返回此套接字绑定的端口的地址，如果尚未绑定则返回null。

    int getReceiveBufferSize()：获取此 ServerSocket 的 SO_RCVBUF 选项的值，该值是从

    ServerSocket 接收的套接字的建议缓冲区大小。

    调用 accept() 方法会返回一个和客户端 Socket 对象相连接的 Socket 对象，

    服务器端的 Socket使用 getOutputStream() 方法获得的输出流

    将指向客户端 Socket 使用 getInputStream() 方法获得那个输入流。

    同样，服务器端的 Socket使用的 getInputStream() 方法获得的输入流将指向客户端 Socket 使用的 getOutputStream() 方法获得的那个输出流。也就是说，当服务器向输出流写入信息时，客户端通过相应的输入流就能读取，反之同样如此。

###Socket 类

>Socket 类表示通信双方中的客户端，用于呼叫远端机器上的一个端口，主动向服务器端发送数据（当连接建立后也能接收数据）。

Socket 的构造方法

    Socket()：无参构造方法。

    Socket(InetAddress address,int port)：创建一个流套接字并将其连接到指定 IP 地址的指定端口。

    Soclcet(InetAddress address,int port,InetAddress localAddr,int localPort)：创建一个套接字并将其连接到指定远程地址上的指定远程端口。

    Socket(String host,int port)：创建一个流套接字并将其连接到指定主机上的指定端口。

    Socket(String host,int port,InetAddress localAddr,int localPort)：创建一个套接字并将其连接到指定远程地址上的指定远程端口。

Socket 的常用方法

    void bind(SocketAddress bindpoint)：将套接字绑定到本地地址。

    void close()：关闭此套接字。

    void connect(SocketAddress endpoint)：将此套接字连接到服务器。

    InetAddress getInetAddress()：返回套接字的连接地址。

    InetAddress getLocalAddress()：获取套接字绑定的本地地址。

    InputStream getInputStream()：返回此套接字的输入流。

    OutputStream getOutputStream()：返回此套接字的输出流。

    SocketAddress getLocalSocketAddress()：返回此套接字绑定的端点地址，如果尚未绑定则返回null。

    SocketAddress getRemoteSocketAddress()：返回此套接字的连接的端点地址，如果尚未连接则返回null。

    int getLoacalPort()：返回此套接字绑定的本地端口。

    int getPort()：返回此套接字连接的远程端口。


###例子：客户端与服务器端的简单通信
