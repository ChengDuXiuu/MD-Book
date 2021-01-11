官网：http://nginx.org/en/docs/

 ## Nginx目录索引 

`ngx_http_autoindex_module`模块处理以斜杠字符('/')结尾的请求，并生成目录列表。
当n`gx_http_index_module`模块找不到索引文件时，通常会将请求传递给模块。

>   大概意思就是将静态资源文件以目录树结构进行呈现
>
>   *   浏览器能解析：点击则直接解析执行。
>   *   3浏览器不能解析：点击则直接下载



![1610364702567](第五章-Nginx模块.assets/1610364702567.png)

![1610364807597](第五章-Nginx模块.assets/1610364807597.png)

`使用步骤：`

*   修改配置文件

    ```txt
     location / {		
     	charset utf-8,gbk;#可以将此设置放置全局即server层面
         root html/ngx_http_autoindex_module;
         autoindex on;
         #index  index.html index.htm;	#注释掉，防止访问项目去到index首页。
    }
    ```

*   效果

    ![1610368200944](第五章-Nginx模块.assets/1610368200944.png)

*   显示文件确切大小

    ```javascript
    语法:	autoindex_exact_size on | off;
    默认值:	
    autoindex_exact_size on;
    上下文:	http, server, location
    ```

    ```bash
    location / {		
        root html/ngx_http_autoindex_module;
        autoindex on;
        autoindex_exact_size off; #显示文件确切大小
        #index  index.html index.htm;	#注释掉，防止访问项目去到index首页。
    
    }
    ```

*   列出文件具体时间

    ```javascript
    语法:	autoindex_localtime on | off;  //on表示当地时区 off为默认 为国际时区
    默认值:	
    autoindex_localtime off;
    上下文:	http, server, location
    ```

    ```bash
    location / {		
        root html/ngx_http_autoindex_module;
        autoindex on;
        autoindex_localtime on;
        autoindex_exact_size off; #显示文件确切大小
        #index  index.html index.htm;	#注释掉，防止访问项目去到index首页。
    
    }
    ```

    ![1610369120259](第五章-Nginx模块.assets/1610369120259.png)

*   实现项目访问同时又实现目录索引

    修改配置文件添加多个location

    ```bash
    location / {		
        root html/dist;
        index  index.html index.htm;
    }
    location /download { #如果使用root则访问的是：html/ngx_http_autoindex_module/download。如果使用的是alias则访问的跟目录即：html/ngx_http_autoindex_module	
        alias html/ngx_http_autoindex_module;
        autoindex on;
        autoindex_localtime on;
        autoindex_exact_size off; #显示文件确切大小
        #index  index.html index.htm;	#注释掉，防止访问项目去到index首页。
    }			
    ```

    

## Nginx状态监控

`ngx_http_stub_status_module`模块提供对基本状态信息的访问。
默认情况下不构建此模块，应使用--with-http_stub_status_module 配置参数启用它 。



`使用步骤：`

*   配置文件中新增location

    ```bash
    location /nginx_status {
        stub_status;
    }
    ```

*   结果

    ![1610369966363](第五章-Nginx模块.assets/1610369966363.png)

*   结果解析

    ```bash
    ### 上图中3 3 5对应上一行的accepts handled requests
    Active connections  # 当前活动客户端连接数，包括Waiting等待连接数。
    accepts             # 已接受总的TCP连接数。
    handled             # 已处理总的TCP连接数。
    requests            # 客户端总的http请求数。
    
    Reading             # 当前nginx读取请求头的连接数。
    Writing             # 当前nginx将响应写回客户端的连接数。
    Waiting             # 当前等待请求的空闲客户端连接数。
    
    # 注意, 一次TCP的连接，可以发起多次http的请求, 如下参数可配置进行验证
    keepalive_timeout  0;   # 类似于关闭长连接
    keepalive_timeout  65;  # 65s没有活动则断开连接
    ```

    



## Nginx访问控制 

 `ngx_http_access_module`模块允许限制对某些客户端地址的访问。

`语法：`

````bash
#允许配置语法
Syntax: allow address | CIDR | unix: | all;
Default:    —
Context:    http, server, location, limit_except

#拒绝配置语法
Syntax: deny address | CIDR | unix: | all;
Default:    —
Context:    http, server, location, limit_except
````

`eg:`

```bash
location /nginx_status { # # 除了网段192.168.1.0中的192.168.1.1和10.1.1.0/16以及2001:0bs8::/32的ip可以进行访问，其余的都不行。匹配规则：过来一个IP(10.1.1.0)先匹配第一个第二个第三个等等，直到匹配上。因此最后添加deny all;很有必要
    stub_status;
    deny 192.168.1.1;
    allow 192.168.1.0/24;
    allow 10.1.1.0/16;
    allow 2001:0bs8::/32;
    deny all;
}
```

`实际中配置：`

```bash
location /nginx_status { # 除了本机其余的都不行
    stub_status;
    deny 127.0.0.1;
    deny all;
}
```



## Nginx资源限制

 `ngx_http_auth_basic_module`模块允许使用HTTP基本身份验证，验证用户名和密码来限制对资源的访问。*

即：访问需要用户名和密码

`语法：`

````bash
#使用HTTP基本身份验证协议启用用户名和密码验证。
Syntax: auth_basic string| off;
Default: auth_basic off;
Context: http, server, location, limit_except

#指定保存用户名和密码的文件
Syntax: auth_basic_user_file file;
Default: -
Context: http, server, location, limit_except
````

`eg:`

*   指定保存用户名和密码的文件，格式如下：

    1.  用户信息文件生成

        ```bash
        #可以使用htpasswd程序或"openssl passwd"命令生成对应的密码;win下可以自己新建一个文件然后使用在线htpasswd网站生成，将用户名密码按照如下格式输入
        name1:passwd1
        name2:passwd2
        
        #使用htpaaswd创建新的密码文件, -c创建新文件 -b允许命令行输入密码
        [root@xuliangwei ~]# yum install httpd-tools
        [root@xuliangwei ~]# htpasswd -b -c /etc/nginx/auth_conf xul
        ```

    2.  修改配置文件











```bash
location /nginx_status { # # 除了网段192.168.1.0中的192.168.1.1和10.1.1.0/16以及2001:0bs8::/32的ip可以进行访问，其余的都不行。匹配规则：过来一个IP(10.1.1.0)先匹配第一个第二个第三个等等，直到匹配上。因此最后添加deny all;很有必要
    stub_status;
    deny 192.168.1.1;
    allow 192.168.1.0/24;
    allow 10.1.1.0/16;
    allow 2001:0bs8::/32;
    deny all;
}
```

`实际中配置：`

```bash
location /nginx_status { # 除了本机其余的都不行
    stub_status;
    deny 127.0.0.1;
    deny all;
}
```











## Nginx访问限制









## Nginx Location

