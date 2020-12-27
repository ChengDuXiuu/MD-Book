## 搭建静态网站原理

> 利用http、URL、fs模块通过不同的URL请求以及请求后缀从而读取到相对路径下的静态文件然后返回到客户端。





## 搭建动态网站原理

* 前后端不分离：静态模板ejs

	* 安装ejs

		```bash
		 cnpm install ejs --save
		```

	* App.js

		```javascript
		const http = require('http')
		const url = require('url')
		const fs = require('fs')
		const ejs = require('ejs')
		
		const hostname = '127.0.0.1'
		const port = 3000
		
		const server = http.createServer()
		server.on('request', (req, res) => {
		    if (req.url.includes('favicon.ico')) return;
		    // todo 解析URL请求路径
		    let urlPath;
		    if (req.url.includes("?")) {
		        urlPath = req.url.split("?")[0].split("/");
		    } else {
		        urlPath = req.url.split("/");
		    }
		    let urlPathName = urlPath[urlPath.length - 1] + '.ejs';
		
		    //从数据库中获取数据
		    let msg = "这是从数据库里面获取到的数据！";
		    let stu = [{
		            name: '学生1号'
		        },
		        {
		            name: '学生2号'
		        },
		        {
		            name: '学生3号'
		        },
		    ]
		    ejs.renderFile(`./view/${urlPathName}`, { msg: msg, stu: stu }, (err, data) => {
		        if (err) {
		            res.writeHead(404, { "Content-type": "text/html;charset='utf-8" });
		            res.write("<head><meta charset=\"UTF-8\"></head>");
		            res.end(`访问错误！！！`);
		            // throw err;
		        }
		        res.writeHead(200, { "Content-type": "text/html;charset='utf-8" })
		        res.end(data);
		    })
		
		    //读取view对应的ejs文件并返回
		    // fs.readFile(`./view/${urlPathName}`, { encoding: 'utf-8' }, (err, data) => {
		    //     if (err) {
		    //         res.writeHead(404, { "Content-type": "text/html;charset='utf-8" });
		    //         res.write("<head><meta charset=\"UTF-8\"></head>");
		    //         res.end(`访问错误！！！`);
		    //         // throw err;
		    //     }
		    //     res.writeHead(200, { "Content-type": "text/html;charset='utf-8" })
		    //     res.end(data);
		    // });
		
		    //使用ejs返回动态模板
		
		
		}).listen(port, hostname, () => {
		    console.log(`服务器运行在 http://${hostname}:${port}/`)
		}) 
		```

	* View/login.ejs

		```ejs
		<!DOCTYPE html>
		<html lang="en">
		
		<head>
		    <meta charset="UTF-8">
		    <title>Title</title>
		</head>
		
		<body>
		    <h1>hello ejs</h1>
		    <span>数据：<%= msg%></span>
		    <ul>
		        <%for(var i=0;i<stu.length;i++){%>
		            <li>
		                <%=stu[i].name%>
		            </li>
		            </li>
		            <%}%>
		    </ul>
		</body>
		
		</html> 
		```

	> ejs语法参考：https://ejs.bootcss.com/

* 前后端分离