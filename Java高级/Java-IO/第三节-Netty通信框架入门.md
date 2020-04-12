### 是什么？

相对于Tomcat这种Web Server服务器。Netty是一个`Nettwork Server`，是处于Web Server更下层的网络框架。简而言之，Netty通过使用Nio的很多新特性，对TCP/UDP编程进行了简化和封装，提供了更容易使用的网络编程接口，让你可以更具自己的需要定制独特的http Server或者FTP Server等等。或者是作为其他框架的高性能的基础通信组件，在分布式中有这广泛的前景，因为多个服务组件需要使用通信或者是远程调用，netty就是一个很好的组件。


### 应答服务器入门

```java
package netty.time;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import java.io.IOException;
import java.net.InetAddress;

/**
 * TODO:<p> 第一节：编写一个应答服务器 <p/>
 *
 * @package: netty.time
 * @Author mac
 * @Date 2020/4/8 8:56 下午
 * @Version V1.0
 **/
public class EchoServer {
    private final int port;
    private final String ip;

    public EchoServer(int port, String ip) {
        this.port = port;
        this.ip = ip;
    }
    public void run() throws InterruptedException {
        //1.创建EventLoopGroup对象并设置到Bootstrap中，EventLoopGroup可以理解为是一个线程池，这个线程池用来处理连接、接受数据、发送数据
        EventLoopGroup group=new NioEventLoopGroup();
        try {
            //2.创建ServerBootstrap对象来启动服务器,然后配置这个对象的相关选项，
            // 如端口、线程模式、事件循环，并且添加逻辑处理程序用来处理业务逻辑
            ServerBootstrap bootstrap=new ServerBootstrap();
            //3.使用nio通道来处理和接受连接
            bootstrap.group(group).channel(NioServerSocketChannel.class)
                    //4.监听服务器IP的端口
                    .localAddress(InetAddress.getByName(this.ip), this.port)
                    //5.相当于nio中将通道注册到select后select轮训处理不同的通道状态如 连接和数据响应
                    .childHandler(new ChannelInitializer<Channel>() {
                        //6.initChannel用来设置ChannelHandler。
                        protected void initChannel(Channel channel) throws Exception {
                            channel.pipeline().addLast(new EchoServerHandler());
                        }
                    });

            //7.sync()会阻塞直到服务器完成绑定
            ChannelFuture future=bootstrap.bind().sync();//会启动线程，因此需要使用future去接受线程执行结果
            System.out.println(EchoServer.class.getName()+"Started and listen on"+future.channel().localAddress());
            future.channel().closeFuture().sync();
        }catch (IOException e){
            e.printStackTrace();
        } finally {
            //
            group.shutdownGracefully().sync();
        }
    }

    private class EchoServerHandler extends ChannelInboundHandlerAdapter{
        /**只有channelRead方法是必须要重写的 */
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
            /**输出到console */
//            ByteBuf in = (ByteBuf) msg;
//            try {
//                while (in.isReadable()) { // (1)
//                    System.out.print("Server received : "+(char) in.readByte());
//                    System.out.flush();
//                }
//            } finally {
//                ReferenceCountUtil.release(msg); // (2)
//            }
            /**返给客户端 */
            System.out.println("Server received : "+msg.toString());
            ctx.write(msg);
        }

        @Override
        public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
            ctx.writeAndFlush(Unpooled.EMPTY_BUFFER).addListener(ChannelFutureListener.CLOSE);
        }

        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
            cause.printStackTrace();
            ctx.close();
        }
    }

}

```

</hr>

```java
package netty.time;

import io.netty.bootstrap.Bootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.ByteBufUtil;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.util.CharsetUtil;
import java.net.InetSocketAddress;

/**
 * TODO:<p> 第一节：应答服务器客户端 <p/>
 *
 * @package: netty.time
 * @Author mac
 * @Date 2020/4/8 9:17 下午
 * @Version V1.0
 **/
public class EchoClient {
    private final String ip;
    private final int port;

    public EchoClient(String ip, int port) {
        this.ip = ip;
        this.port = port;
    }

    public void start() throws InterruptedException {
        //0.创建EventLoopGroup对象并设置到Bootstrap中，EventLoopGroup可以理解为是一个线程池，这个线程池用来处理连接、接受数据、发送数据
        EventLoopGroup group=new NioEventLoopGroup();

        try {
            //1.创建Bootstrap对象用来引导启动客户端
            Bootstrap bootstrap=new Bootstrap();
            bootstrap.group(group).channel(NioSocketChannel.class)
                    .remoteAddress(new InetSocketAddress(this.ip,this.port))
                    //2.添加一个ChannelHandler，客户端成功连接服务器后就会被执行
                    .handler(new ChannelInitializer<SocketChannel>() {
                        protected void initChannel(SocketChannel socketChannel) throws Exception {
                            socketChannel.pipeline().addLast(new EchoClientHandler());
                        }
                    });
            //3.调用Bootstrap.connect()来连接服务器,阻塞连接
            ChannelFuture future=bootstrap.connect().sync();
            future.channel().closeFuture().sync();
        } finally {
            group.shutdownGracefully().sync();
        }
    }

    /**SimpleChannelInboundHandler相比ChannelInboundHandlerAdapter不需要负责释放资源，SimpleChannelInboundHandler会在
    完成channelRead0后释放消息。为什么在服务器中不使用SimpleChannelInboundHandler呢？因为服务器要返回相同的消息给客户端，
     在服务器执行完成写操作之前不能释放调用读取到的消息，因为写操作是异步的，一旦写操作完成后，Netty中会自动释放消息。*/
    private class EchoClientHandler  extends SimpleChannelInboundHandler<ByteBuf> {

        @Override
        public void channelActive(ChannelHandlerContext ctx) throws Exception {
            ctx.writeAndFlush(Unpooled.copiedBuffer("Netty rocks !", CharsetUtil.UTF_8));
        }

        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
            cause.printStackTrace();
            ctx.close();
        }

        protected void channelRead0(ChannelHandlerContext channelHandlerContext, ByteBuf byteBuf) throws Exception {
            System.out.println("Client received : "+ ByteBufUtil.hexDump(byteBuf.readBytes(byteBuf.readableBytes())));
        }
    }
}

```
>![netty-应答服务器](/assets/netty-应答服务器.jpg)

### Netty核心类(围绕上面的应答案例)

1. Bootstrap or ServerBootstrap

    >一个Netty程序开始于Bootstrap类，Bootstrap类是Netty提供的一个可以通过简单配置来设置或"引导"程序的一个很重要的类。Netty中设计了Handlers来处理特定的"event"和设置Netty中的事件，从而来处理多个协议和数据。事件可以描述成一个非常通用的方法，因为你可以自定义一个handler,用来将Object转成byte[]或将byte[]转成Object；也可以定义个handler处理抛出的异常。

     >你会经常编写一个实现ChannelInboundHandler的类，ChannelInboundHandler是用来接收消息，当有消息过来时，你可以决定如何处理。当程序需要返回消息时可以在ChannelInboundHandler里write/flush数据。可以认为应用程序的业务逻辑都是在ChannelInboundHandler中来处理的，业务逻辑生命周期在ChannelInboundHandler中。

* EventLoop(Nio中选择器)

* EventLoopGroup

* ChannelPipeline

* Channel

* ChannelInitializer

* ChannelHandler

     >Netty连接客户端端或绑定服务器需要知道如何发送或接收消息，这是通过不同类型的handlers来做的，多个Handlers是怎么配置的？Netty提供了ChannelInitializer类用来配置Handlers。ChannelInitializer是通过ChannelPipeline来添加ChannelHandler的，如发送和接收消息，这些Handlers将确定发的是什么消息。ChannelInitializer自身也是一个ChannelHandler，在添加完其他的handlers之后会自动从ChannelPipeline中删除自己。所有的Netty程序于ChannelPipeline。ChannelPipeline和EventLoop和EventLoopGroup密切相关，因为它们三个都和事件处理相关，所以这就是为什么它们处理IO的工作由EventLoop管理的原因。

* Future or ChannelFuture


    >Netty中所有的IO操作都是异步执行的，例如你连接一个主机默认是异步完成的；写入/发送消息也是同样是异步。也就是说操作不会直接执行，而是会等一会执行，因为你不知道返回的操作结果是成功还是失败，但是需要有检查是否成功的方法或者是注册监听来通知；Netty使用Futures和ChannelFutures来达到这种目的。Future注册一个监听，当操作成功或失败时会通知。ChannelFuture封装的是一个操作的相关信息，操作被执行时会刻返回ChannelFuture。

### Netty核心概念 - Channels,Events and Input/Output(IO)

Netty是一个非阻塞、事件驱动的网络框架。Netty实际上是使用多线程处理IO事件，对于熟悉多线程编程的读者可能会需要同步代码。这样的方式不好，因为同步会影响程序的性能，Netty的设计保证程序处理事件不会有同步。

一个EventLoopGroup和一个Channel关联一个单一的EventLoop，Netty中的EventLoopGroup包含一个或多个EventLoop，而EventLoop就是一个Channel执行实际工作的线程。<font color=ffaa00>EventLoop总是绑定一个单一的线程</font>，在其生命周期内不会改变。

当注册一个Channel后，<font color=ffaa00>Netty将这个Channel绑定到一个EventLoop(nio中select)，在Channel的生命周期内总是被绑定到一个EventLoop。在Netty IO操作中，你的程序不需要同步，因为一个指定通道的所有IO始终由同一个线程来执行。</font>


### Netty Reactor 模型

1、 单线程模型

    用户发起 IO 请求到 Reactor 线程Ractor 线程将用户的 IO 请求放入到通道，然后再进行后续处理处理完成后， Reactor 线程重新获得控制权，
    继续其他客户端的处理。即nio中的select选择器模型。单线程非阻塞模式。
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="/assets/netty单线程.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">netty单线程模型</div>
</center>


缺点：

1. 但单线程的 Reactor 模型每一个用户事件都在一个线程中执行
2. 性能有极限，不能处理成百上千的事件
3. 当负荷达到一定程度时，性能将会下降
4. 某一个事件处理器发生故障，不能继续处理其他事件


2、 多线程模型

    Reactor 多线程模型是由一组 NIO 线程来处理 IO 操作（之前是单个线程），所以在请求处理上会比上一中模型效率更高，
    可以处理更多的客户端请求。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="/assets/netty多线程.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">netty多线程模型</div>
</center>

缺点：

1. 但是如果并发仍然很大， Reactor 仍然无法处理大量的客户端请求

3、 主从多线程模型

    这种线程模型是 Netty 推荐使用的线程模型。适用于高并发场景， 一组线程池接收请求， 一组线程池处理 IO。并且也是netty官方给定案例中使用的模型。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="/assets/netty主从多线程.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">netty主从多线程模型</div>
</center>



### 案例 - 基于websocket的聊天室

```java
package netty.webScket;

import cn.hutool.log.Log;
import cn.hutool.log.LogFactory;
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.group.ChannelGroup;
import io.netty.channel.group.DefaultChannelGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.http.HttpObjectAggregator;
import io.netty.handler.codec.http.HttpServerCodec;
import io.netty.handler.codec.http.websocketx.TextWebSocketFrame;
import io.netty.handler.codec.http.websocketx.WebSocketServerProtocolHandler;
import io.netty.handler.stream.ChunkedWriteHandler;
import io.netty.util.concurrent.GlobalEventExecutor;
import java.time.LocalDateTime;

/**
 * TODO:<p>  <p/>
 *
 * @package: netty.webScket
 * @Author mac
 * @Date 2020/4/12 8:42 下午
 * @Version V1.0
 **/
public class WebsocketServer {
    private static Log log= LogFactory.get();
    public static void main(String[] args) {
        //初始化主线程池（boss 线程池-连接请求）
        NioEventLoopGroup bossGroup=new NioEventLoopGroup();
        //初始化从线程池(worker 线程池-处理任务数据)
        NioEventLoopGroup workGroup=new NioEventLoopGroup();

        try {
            // 创建服务器启动器
            ServerBootstrap bootstrap= new ServerBootstrap();
            // 指定使用主线程池和从线程池
            bootstrap.group(bossGroup,workGroup)
                    // 指定使用 Nio 通道类型
                    .channel(NioServerSocketChannel.class)
                    // 指定通道初始化器加载通道处理器
                    .childHandler(new WsServerInitializer());

            // 绑定端口号启动服务器，并等待服务器启动
            // ChannelFuture 是 Netty 的回调消息
            ChannelFuture future = bootstrap.bind(5555).sync();
            // 等待服务器 socket 关闭
            future.channel().closeFuture().sync();
        }catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            // 优雅关闭 boos 线程池和 worker 线程池
            bossGroup.shutdownGracefully();
            workGroup.shutdownGracefully();
        }


    }

    private static class WsServerInitializer extends ChannelInitializer<SocketChannel> {
        @Override
        protected void initChannel(SocketChannel ch) throws Exception {
            /** 其中通道（pipeline）中添加的类相当于拦截器，而通道类似于队列，即每个走该通道的请求都会经过拦截器对其进行处理，原理和tcp/ip协议中的数据包封装和拆解类似*/
            ChannelPipeline pipeline=ch.pipeline();
            // 用于支持 Http 协议
            // websocket 基于 http 协议，需要有 http 的编解码器
            pipeline.addLast(new HttpServerCodec());
            // 对写大数据流的支持
            pipeline.addLast(new ChunkedWriteHandler());
            // 添加对 HTTP 请求和响应的聚合器:只要使用 Netty 进行 Http 编程都需要使用
            // 对 HttpMessage 进行聚合，聚合成 FullHttpRequest 或者 FullHttpResponse
            // 在 netty 编程中都会使用到 Handler
            pipeline.addLast(new HttpObjectAggregator(1024 * 64));
            // ---------支持 Web Socket -----------------
            // websocket 服务器处理的协议，用于指定给客户端连接访问的路由: /ws
            // 本 handler 会帮你处理一些握手动作: handshaking(close, ping, pong) ping +pong = 心跳
            // 对于 websocket 来讲，都是以 frames 进行传输的，不同的数据类型对应的 frames 也不同
            pipeline.addLast(new WebSocketServerProtocolHandler("/ws"));
            // 添加自定义的 handler
            pipeline.addLast(new ChatHandler());

        }

    }

    private static class ChatHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {
        //用来记录和管理连接进来的客户端。类似于bio中的map(存放socket流和客户端socket)
        private ChannelGroup clients=new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);
        @Override
        protected void channelRead0(ChannelHandlerContext channelHandlerContext, TextWebSocketFrame textWebSocketFrame) throws Exception {
            //获取从客户端传输过来的消息
            String text = textWebSocketFrame.text();
            log.info("接收到的数据: {} ",text);
            //将接收到消息发送到所有客户端
            for (Channel client : clients) {
                //注意所有的 websocket 数据都应该以 TextWebSocketFrame 进行封装
                client.writeAndFlush(new TextWebSocketFrame("服务器收到的消息 ："+ LocalDateTime.now()+",消息为 ："+text));
            }
        }
        /**
         * 当客户端连接服务端之后（打开连接）
         * 获取客户端的 channel，并且放入到 ChannelGroup 中去进行管理
         * @param ctx
         * @throws Exception
         */
        @Override
        public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
            System.out.println("ehllo");
            clients.add(ctx.channel());
        }

        @Override
        public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
            // 当触发 handlerRemoved， ChannelGroup 会自动移除对应客户端的 channel
            //clients.remove(ctx.channel());
            // asLongText()——唯一的 ID
            // asShortText()——短 ID（有可能会重复)
            log.info("客户端断开，channel对应的长id为 ：{} ",ctx.channel().id().asLongText());
            log.info("客户端断开，channel对应的短id为 ：{} ",ctx.channel().id().asShortText());

        }
    }
}

```
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title></title>
    <script type="text/javascript">

        document.addEventListener('plusready', function(){
            //console.log("所有plus api都应该在此事件发生后调用，否则会出现plus is undefined。")

        });

    </script>
</head>
<body>
<div>发送消息</div>
<input type="text" id="msgContent" />
<input type="button" value="点击发送" onclick="CHAT.chat()"/>
<div>接收消息:</div>
<div id="recMsg" style="background-color: gainsboro;"></div>
<script type="application/javascript">
    window.CHAT = {
        socket: null,
        init: function() {
            // 判断浏览器是否支持 websocket
            if(window.WebSocket) {
                // 支持 WebScoekt
                // 连接创建 socket，注意要添加 ws 后缀
                CHAT.socket = new WebSocket("ws://127.0.0.1:5555/ws");
                CHAT.socket.onopen = function() {
                    console.log("连接建立成功");
                };
                CHAT.socket.onclose = function() {
                    console.log("连接关闭")
                };
                CHAT.socket.onerror = function() {
                    console.log("发生错误");
                };
                CHAT.socket.onmessage = function(e) {
                    console.log("接收到消息:" + e.data);
                    var recMsg = document.getElementById("recMsg");
                    var html = recMsg.innerHTML;
                    recMsg.innerHTML = html + "<br/>" + e.data;
                };
            }
            else {
                alert("浏览器不支持 websocket 协议");
            }
        },
        chat: function() {
            var msg = document.getElementById("msgContent");
            CHAT.socket.send(msg.value);
        }
    }
    CHAT.init();
</script>
</body>
</html>

```
