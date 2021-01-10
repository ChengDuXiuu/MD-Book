## 日志管理

`语法：`

```bash
# 配置语法: 包括: error.log access.log
Syntax: log_format name [escape=default|json] string ...;
Default: log_format combined "...";
Context: http|server|location|if in location|limit_except  # 这里指层即配置文件中server以及http等代码块
```

> 即建议每个server代码块配置不同的项目并且配置不同的日志文件

`配置文件中默认日志输出格式`

```bash
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;
```

> 127.0.0.1 - - [10/Jan/2021:21:59:59 +0800] "GET /font/static/fonts/element-icons.535877f.woff HTTP/1.1" 304 0 "http://localhost:8080/font/static/css/app.993c80e3b7f02ed6366568ac17a9909e.css" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"
> 127.0.0.1 - - [10/Jan/2021:21:59:59 +0800] "GET /font/static/ScreenImage/screen09.jpg HTTP/1.1" 200 909762 "http://localhost:8080/font/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"

`linux查看日志文件位置`

```bash
nginx -V
```

`日志格式中变量`

```bash
$remote_addr        # 记录客户端IP地址
$remote_user        # 记录客户端用户名
$time_local         # 记录通用的本地时间
$time_iso8601       # 记录ISO8601标准格式下的本地时间
$request            # 记录请求的方法以及请求的http协议
$status             # 记录请求状态码(用于定位错误信息)
$body_bytes_sent    # 发送给客户端的资源字节数，不包括响应头的大小
$bytes_sent         # 发送给客户端的总字节数
$msec               # 日志写入时间。单位为秒，精度是毫秒。
$http_referer       # 记录从哪个页面链接访问过来的
$http_user_agent    # 记录客户端浏览器相关信息
$http_x_forwarded_for #记录客户端IP地址
$request_length     # 请求的长度（包括请求行， 请求头和请求正文）。
$request_time       # 请求花费的时间，单位为秒，精度毫秒
# 注:如果Nginx位于负载均衡器，nginx反向代理之后， web服务器无法直接获取到客 户端真实的IP地址。
# $remote_addr获取的是反向代理的IP地址。 反向代理服务器在转发请求的http头信息中，
# 增加X-Forwarded-For信息，用来记录客户端IP地址和客户端请求的服务器地址。
```





## 日志切割

如果一个项目只使用一个日志文件，项目使用周期过长日志文件过大会导致打不开以及查看不方便等问题。



```bash
[root@nginx conf.d]# cat /etc/logrotate.d/nginx
/var/log/nginx/*.log {
        daily                   # 每天切割日志
        missingok               # 日志丢失忽略
        rotate 52               # 日志保留52天
        compress                # 日志文件压缩
        delaycompress           # 延迟压缩日志
        notifempty              # 不切割空文件
        create 640 nginx adm    # 日志文件权限
        sharedscripts
        postrotate      # 切割日志执行的命令
                if [ -f /var/run/nginx.pid ]; then
                        kill -USR1 `cat /var/run/nginx.pid`
                fi
        endscript
}
```

