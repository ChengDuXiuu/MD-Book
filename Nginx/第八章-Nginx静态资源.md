## 静态资源配置

* 静态资源类型

	Nginx作为静态资源 Web 服务器器部署配置, 传输⾮非常的⾼高效, 常常⽤用于静态资源处理理, 请求, 动静
	分离

	![image-20210117212937893](第八章-Nginx静态资源.assets/image-20210117212937893.png)

	> 非服务器器动态运⾏行行⽣生成的⽂文件属于静态资源

	| 类型           | 种类                |
	| -------------- | ------------------- |
	| 浏览器器端渲染 | HTML、 CSS、 JS     |
	| 图⽚           | JPEG、 GIF、 PNG    |
	| 视频           | FLV、 Mp4           |
	| ⽂件           | TXT、任意下载⽂文件 |

* 静态资源场景

	> 资源中心使用Nginx部署多地服务器。用户使用DNS就近访问附近的服务器

![image-20210117214240863](第八章-Nginx静态资源.assets/image-20210117214240863.png)



## 静态资源配置

* 文件读取⾼效 `sendfile`

	````bash
	Syntax:	sendfile	on	|	off;
	Default:	sendfile	off;
	Context:	http,	server,	location,	if in	location
	````

* 提高⽹网络传输效率 `nopush`

	````bash
	Syntax:	tcp_nopush	on	|	off;
	Default:	tcp_nopush	off;
	Context:	http,	server,	location
	作⽤用:	sendfile开启情况下,	提高网络包的'传输效率' 
	````

* 与` tcp_nopush` 之对应的配置 `tcp_nodelay`

	````bash
	Syntax:	tcp_nodelay	on	|	off;
	Default:	tcp_nodelay	on;
	Context:	http,	server,	location
	作⽤用:	在keepalive连接下,提⾼网络的传输'实时性'
	````

	

## 静态资源文件压缩

正常情况下，请求静态文件返回静态文件，如果静态文件过打则会出现浏览器卡死。Nginx压缩是Nginx 将响应报⽂资源发送至客户端之前可以启⽤压缩功能，这能够有效地节约带宽，并提⾼响应⾄客户端的速度。

简单来说，就是响应报文在服务器先压缩(CPU消耗)后发送至客户端，然后客户端解压缩(浏览器是支持解压缩)

![image-20210117220920823](第八章-Nginx静态资源.assets/image-20210117220920823.png)



* `gzip` 压缩配置语法

	````bash
	Syntax:	gzip	on	|	off;
	Default:	gzip	off;
	Context:	http,	server,	location,	if	in	location
	作用:	传输压缩
	````

* `gzip`压缩比率配置语法

	````bash
	Syntax:	gzip_comp_level	level;
	Default:	gzip_comp_level	1;
	Context:	http,	server,	location
	作⽤用:	压缩本身⽐比较耗费服务端性能
	````

* `gzip `压缩协议版本

	````bash
	Syntax:	gzip_http_version	1.0	|	1.1;
	Default:	gzip_http_version	1.1;
	4.静态资源⽂文件压缩Context:	http,	server,	location
	作⽤用:	压缩使⽤用在http哪个协议,	主流版本1.1
	````

* 扩展压缩模块

	````bash
	Syntax:	gzip_static	on	|	off	|	always;
	Default:	gzip_static	off;
	Context:	http,	server,	location
	作⽤用:	预读gzip功能
	````

* Eg:

	````bash
	[root@Nginx	conf.d]#	mkdir	-p	/soft/code/images
	[root@Nginx	conf.d]#	cat	static_server.conf	
	server	{
	  listen	80;
	  server_name	192.168.56.11;
	  sendfile on;
	  access_log /var/log/nginx/static_access.log main;
	  location	~	.*\.(jpg|gif|png)$	{
	    gzip on;
	    gzip_http_version	1.1;
	    gzip_comp_level	2;
	    gzip_types text/plain	application/json	application/x-javascript	app
	    lication/css	application/xml	application/xml+rss text/javascript	application/x-http
	    d-php	image/jpeg	image/gif	image/png;
	    root /soft/code/images;
		}
	}
	````

	