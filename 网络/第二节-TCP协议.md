TCP 用于应用程序之间的通信。
当应用程序希望通过 TCP 与另一个应用程序通信时，它会发送一个通信请求。这个请求必须被送到一个确切的地址。在双方“三次握手”之后，TCP 将在两个应用程序之间建立一个全双工 (full-duplex) 的通信。这个全双工的通信将占用两个计算机之间的通信线路，直到它被一方或双方关闭为止。

###TCP三次握手
所谓三次握手（Three-Way Handshake）即建立TCP连接，就是指建立一个TCP连接时，
需要客户端和服务端总共发送3个包以确认连接的建立。
在socket编程中，这一过程由客户端执行connect来触发，整个流程如下图所示：
![失效](/网络/资料/tcp三次握手.jpg)

ACK：确认标志。确认值(Acknowledgement)，为1便是确认连接。SYN：同步标志。表明同步序列编号栏有效。
FIN：结束标志。
ack：确认编号(Acknowledgement Number)，即接收到的上一次远端主机传来的seq然后
+1，再发送给远端主机。提示远端主机已经成功接收上一次所有数据。

>1）第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给
Server，Client进入SYN_SENT状态，等待Server确认。

>2）第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将
标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以
确认连接请求，Server进入SYN_RCVD状态。

>3）第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志
位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否
为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，
随后Client与Server之间可以开始传输数据了。


###TCP四次挥手
所谓四次挥手（Four-Way Wavehand）即终止TCP连接，就是指断开一个TCP连接时，需要客
户端和服务端总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服
务端任一方执行close来触发，整个流程如下图所示：
![失效](/网络/资料/tcp四次挥手.jpg)

由于TCP连接时全双工的，因此，每个方向都必须要单独进行关闭，这一原则是当一方完成
数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上
没有数据流动了，即不会再收到数据了，但是在这个TCP连接上仍然能够发送数据，直到这
一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上
图描述的即是如此。

>1）第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入
FIN_WAIT_1状态。

>2）第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。

>3）第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入
LAST_ACK状态。

>4）第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给
Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。
