1.  npm init

2.  npm install --save express

3.  npm install --save jsonp

4.  示例

    ````html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <!--script标签不存在域的限制-->
    <!--跨域请求-->
    <script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
    <!--本地请求 不用跨域-->
    <script src="./index.jsonp.js"></script>
    </body>
    </html>
    ````

    ```javascript
    $.ajax({
        url:'http://localhost:8001/list',
        method: 'get',
        dataType:'jsonp',
        success:data => {
            console.log(data);
        }
    })
    ```

    ```javascript
    const express = require('express')
    const app = express()
    
    app.listen(8001,()=>{
        console.log('OK !');
    })
    
    app.get('/list', (req, res)=> {
        console.log("list");
        let {
            callback = Function.prototype
        } = req.query;
    
        let data = {
            code: 0,
            message: '成功执行！'
        };
        res.send(`${callback}(${JSON.stringify(data)})`);
    })
    ```

    

