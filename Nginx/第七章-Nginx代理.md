## 代理

代理一词往往并不陌生, 该服务我们常常用到如(代理理财、代理租房、代理收货等等)，如下图所示

![img](第七章-Nginx代理.assets/15239420127242.jpg)

 

在没有代理模式的情况下，客户端和Nginx服务端，都是客户端直接请求服务端，服务端直接响应客户端
![img](第七章-Nginx代理.assets/15427999258261.jpg)

那么在互联网请求里面, 客户端往往无法直接向服务端发起请求, 那么就需要用到代理服务, 来实现客户端和服务通信，如下图所示
![img](第七章-Nginx代理.assets/15378036144456.jpg)



## Nginx代理模式

Nginx作为代理服务, 按照应用场景模式进行总结，代理分为正向代理、反向代理。

* 正向代理

  主要用于内部上网，客户端<-->代理<-->服务端。

  > * 由于某先原因我们不能直接访问服务端比如Google等等，因为谷歌被墙了，如果有一台服务器(甭管国内还是国外)可以访问Google并且没有被墙，这个时候你就可以使用这台服务器作为跳板，跳转到Google服务器上。
  > * 还有一种情况就是局域网访问外网，之前我们是通过锚现在路由器本质是一样的，都是代理性质，我们访问路由器或者锚，然后跳转到外网。

  特点：你要访问的服务器(Google服务器)并不知道哪个用户在访问它，只知道你代理的服务器(锚，路由器等等)在访问他，需要用户去折腾。

* 反向代理

  用于公司集群架构中。客户端-->代理<-->服务端。

  > 反向代理用户不需要折腾，服务端去折腾，比如百度阿里等等他们肯定不止一台服务器，但是对于客户来说只需要访问服务端的不同地区的代理服务器即可。

* 区别

	1. 服务的对象不一样。正向代理代理的对象的是客户端，为客户端服务，一般用于上网。
	2. 反向代理代理的是服务端，为服务端服务，一般用于负载均衡。



## Nginx代理支持协议

* Nginx作为代理服务，可支持的代理协议非常的多，具体如下图

![img](第七章-Nginx代理.assets/15427911480446.jpg)

* 如果将Nginx作为反向代理服务，常常会用到如下几种代理协议，如下图所示

	![img](第七章-Nginx代理.assets/15427930199904.jpg)

* 反向代理模式与Nginx代理模块总结如表格所示

| 反向代理模式           | Nginx配置模块           |
| :--------------------- | ----------------------- |
| http、websocket、https | ngx_http_proxy_module   |
| fastcgi                | ngx_http_fastcgi_module |
| uwsgi                  | ngx_http_uwsgi_module   |
| grpc                   | ngx_http_v2_module      |



## Nginx代理配置

1. Nginx 代理理配置语法

	```bash
	Syntax:	proxy_pass	URL;
	Default:				—
	Context:				location,	if	in	location,	limit_except
	  
	http://localhost:8000/uri/
	http://192.168.56.11:8000/uri/
	http://unix:/tmp/backend.socket:/uri/
	```

	

2. 类似于 nopush 缓冲区

  ```bash
  //尽可能收集所有头请求,	
  Syntax:	proxy_buffering	on	|	off;
  Default:				
  proxy_buffering	on;
  Context:				http,	server,	location
  //扩展:
  proxy_buffer_size	
  proxy_buffers	
  proxy_busy_buffer_size
  ```

3. 跳转重定向

  ```bash
  Syntax:	proxy_redirect	default;
  proxy_redirect	off;proxy_redirect	redirect	replacement;
  Default:				proxy_redirect	default;
  Context:				http,	server,	location
  ```

4. 头信息

  ```bash
  Syntax:	proxy_set_header	field	value;
  Default:				proxy_set_header	Host	$proxy_host;
  												proxy_set_header	Connection	close;
  Context:				http,	server,	location
  //扩展:	
  proxy_hide_header
  proxy_set_body
  ```

5. 代理到后端连接超时

  ```bash
  Syntax:	proxy_connect_timeout	time;
  Default:	proxy_connect_timeout	60s;
  Context:	http,	server,	location
  //扩展
  proxy_read_timeout		//以及建⽴立
  proxy_send_timeout		//服务端请求完,	发送给客户端时间
  ```

6. 常见配置

  ```bash
  [root@Nginx	~]#	vim	/etc/nginx/proxy_params
  proxy_redirect	default;
  proxy_set_header	Host	$http_host;
  proxy_set_header	X-Real-IP	$remote_addr;
  proxy_set_header	X-Forwarded-For	$proxy_add_x_forwarded_for;
  proxy_connect_timeout	30;
  proxy_send_timeout	60;
  proxy_read_timeout	60;
  proxy_buffer_size	32k;
  proxy_buffering	on;
  proxy_buffers	4	128k;
  proxy_busy_buffers_size	256k;
  proxy_max_temp_file_size	256k;//具体location实现
  location	/	{
  				proxy_pass	http://127.0.0.1:8080;
  				include	proxy_params;
  }
  ```

  