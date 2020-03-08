### 通过端口启动

启动服务

    redis-server --port 端口号
连接

    redis-cli -p 端口号

### 通过配置文件启动

拷贝redis.cof修改data目录以及端口号等

启动Redis

    redis-server 配置文件路径

配置文件详解：https://blog.csdn.net/maqingbin8888/article/details/81388802  

### 多Redis通过配置文件启动

新建目录Redis1目录并拷贝Redis放入其中(这个时候任然可以启动)
再新建目录Redis2目录并拷贝Redis放入其中并修改配置文件即可

    Redis-server 配置文件路径

启动cli

    redis-cli -p 端口号


### 常用命令

redis-cli -h ip > > 远程连接

redis-cli -p > > 端口启动

redisp-cli -h ip -p port


### Tips

Mac下通过brew安装redis
    bin目录是在/usr/local/Cellar/redis
    cof目录是在/usr/local/etc下
    data目录是在/usr/local/var/db/redis/
