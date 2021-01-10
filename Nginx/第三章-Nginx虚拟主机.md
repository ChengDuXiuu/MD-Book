## 解决什么问题？

一般来讲，一个nginx负责一个静态项目，如果有很多个静态项目则需要多态服务器搭建多个nginx环境并部署多个前端项目，势必会造成服务器资源的浪费。虚拟主机`就是来解决这种问题的。



## 虚拟主机

1. 基于主机多IP方式

	* 多网卡对应多IP
		* 配置文件中配置多个server代码块，并使用listen监听不同的IP:端口
	* 单网卡对应多IP
		* 添加IP
		* 配置文件中配置多个server代码块，并使用listen监听不同的IP:端口

2. 基于端口的配置方式(企业内部用)

	* 配置文件中添加多个server代码块，使用listen监听不同的端口以及使用location来配置不同的项目

		```javascript
		server {
		        listen       8080;
		        location / {
		            root   html;
		            index  index.html index.htm;
		        }
		}
		server {
		        listen       8081;
		        location /dist {
		            root   html/dist;
		            index  index.html index.htm;
		        }
		}
		server {
		        listen       8082;
		        location /font {
		            root   html/font;
		            index  index.html index.htm;
		        }
		}
		```

		

3. 基于多个hosts名称方式(多域名)

	* 配置文件中添加多个server代码块，使用server_name来显示不同的域名

		```javascript
		server {
		        listen       8080;
		  			server_name test1.com;
		        location / {
		            root   html/test1;
		            index  index.html index.htm;
		        }
		}
		server {
		        listen       8080;
		  			server_name test2.com;
		        location /test2 {
		            root   html/dist;
		            index  index.html index.htm;
		        }
		}
		server {
		        listen       8080;
		  			server_name test3.com;
		        location /test3 {
		            root   html/font;
		            index  index.html index.htm;
		        }
		}
		```

	* 修改系统hosts文件

		```hosts
		127.0.0.1 test1
		127.0.0.1 test2
		127.0.0.1 test3
		
		```

		

	