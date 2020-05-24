### 网络通信协议分层
应用层：

1、HTTP（Hypertext Transfer Protocol 超文本传输协议，显示网页）

2、DNS（Domain Name System 将域名和IP地址相互映射）

3、FTP（File Transfer Protocol 用于文件传输的协议）

4、SFTP（SSH File Transfer Protocol安全文件传输）

5、SCP（Secure copy，based on SSH 文件拷贝）

6、SSH （Secure Shell 远程登录以及网络服务提供安全通道 可替代telnet进行安全登录）

通信层：

1、TCP（Transmission Control Protocol 三次握手传输协议）
UDP

网络层：

1、IP（Internet Protocol）

2、ICMP（Internet Control Message Protocol，主要用于路由发送错误报告）

链接层：

1、MAC（media access control）

参考资料：http://desert3.iteye.com/blog/1684130

<code><font color=#ff85c0 size=4>FTP</font></code>

FTP（File Transfer Protocol）：是TCP/IP网络上两台计算机传送文件的协议，FTP是在TCP/IP网络和INTERNET上最早使用的协议之一，它属于网络协议组的应用层。FTP客户机可以给服务器发出命令来下载文件，上载文件，创建或改变服务器上的目录。相比于HTTP，FTP协议要复杂得多。复杂的原因，是因为FTP协议要用到两个TCP连接，一个是命令链路，用来在FTP客户端与服务器之间传递命令；另一个是数据链路，用来上传或下载数据。FTP是基于TCP协议的，因此iptables防火墙设置中只需要放开指定端口（21 + PASV端口范围）的TCP协议即可。

FTP工作模式：

1、PORT（主动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，客户端在命令链路上用PORT命令告诉服务器：“我打开了一个1024+的随机端口，你过来连接我”。于是服务器从20端口向客户端的1024+随机端口发送连接请求，建立一条数据链路来传送数据。

2、PASV（Passive被动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，服务器在命令链路上用PASV命令告诉客户端：“我打开了一个1024+的随机端口，你过来连接我”。于是客户端向服务器的指定端口发送连接请求，建立一条数据链路来传送数据。

3、PORT方式，服务器会主动连接客户端的指定端口，那么如果客户端通过代理服务器链接到internet上的网络的话，服务器端可能会连接不到客户端本机指定的端口，或者被客户端、代理服务器防火墙阻塞了连接，导致连接失败。PASV方式，服务器端防火墙除了要放开21端口外，还要放开PASV配置指定的端口范围。

<code><font color=#ff85c0 size=4>SFTP</font></code>

SFTP（Secure File Transfer Protocol）：安全文件传送协议。可以为传输文件提供一种安全的加密方法。SFTP与 FTP有着几乎一样的语法和功能。SFTP为SSH的一部份，是一种传输文件到服务器的安全方式。在SSH软件包中，已经包含了一个叫作SFTP(Secure File Transfer Protocol)的安全文件传输子系统，SFTP本身没有单独的守护进程，它必须使用sshd守护进程（端口号默认是22）来完成相应的连接操作，所以从某种意义上来说，SFTP并不像一个服务器程序，而更像是一个客户端程序。SFTP同样是使用加密传输认证信息和传输的数据，所以，使用SFTP是非常安全的。但是，由于这种传输方式使用了加密/解密技术，所以传输效率比普通的FTP要低得多，如果您对网络安全性要求更高时，可以使用SFTP代替FTP。
 1 登陆远程主机：  
 2 sftp user@host  
 3 针对本机的命令都加上l:  
 4 lcd，lpwd  
 5 将本机文件上传到远程：  
 6 put filename.txt [some/directory]  
 7 将当前文件夹下的文件上传到远程：  
 8 mput *.* // multiple  
 9 下载远程文件到本地:  
10 get filename.file [some/directory]  
11 下载目录下所有远程文件到本地：  
12 mget *.* [some/directory]  
13 帮助：  
14 ?  
15 退出：  
16 bye/exit/quit  

<code><font color=#ff85c0 size=4>SCP</font></code>

SCP（Secure Copy）：SCP就是Secure copy，是用来进行远程文件复制的，并且整个复制过程是加密的。数据传输使用ssh，并且和使用和ssh相同的认证方式，提供相同的安全保证。

 1 拷贝本地文件到远程：  
 2 scp filename.txt user@host:some/directory  
 3 拷贝本地文件到远程，使用指定端口：  
 4 scp -P 2234 filename.txt user@host:some/directory  
 5 拷贝多个文件到远程home：  
 6 scp filename1.txt filename2.txt user@host:~  
 7 拷贝远程文件到本地：  
 8 scp user@host:directory/filename.txt  /directory  
 9 拷贝远程文件夹到本地：  
10 scp -r user@host:directory/folder  .  
11 拷贝远程文件到远程：  
12 scp user@host1:directory/filename.txt  user@host1:directory  


<code><font color=#ff85c0 size=4>比较：</font></code>

FTP 基于TCP来传输文件，明文传输用户信息和数据。

SFTP 基于SSH来加密传输文件，可靠性高，可断点续传。

SCP 是基于SSH来加密拷贝文件，但要知道详细目录，不可断点续传。
