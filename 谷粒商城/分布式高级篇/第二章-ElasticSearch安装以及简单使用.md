## Docker安装ElasticSearch和kibana

1. 拉取

	```bash
	docker pull elasticsearch:7.4.2
	docker pull kibana:7.4.2
	```

	

2. ElasticSearch数据卷

	```bash
	# 1. 在宿主机中创建配置文件以及数据文件
	mkdir -p /root/elasticsearch/config
	mkdir -p /root/elasticsearch/data
	# 2. 写入内容 :后面一定要有空格
	echo "http.host: 0.0.0.0" >> /root/elasticsearch/config/elasticsearch.yml
	# 3. 数据挂载
	docker run --privileged=true -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512m -Xmx512m" -v /root/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /root/elasticsearch/data:/usr/share/elasticsearch/data -v /root/elasticsearch/plugins:/usr/share/elasticsearch/plugins -d --name es elasticsearch:7.4.2
	```

	

3. 修改宿主机es目录权限

	```bash
	chmod -R 777 /root/elasticsearch/
	```

	

4. 访问 宿主机IP:9200

	![image-20210127203308661](第二章-ElasticSearch安装以及简单使用.assets/image-20210127203308661.png)



5. 使用postman测试ElasticSearch

	![image-20210127204219324](第二章-ElasticSearch安装以及简单使用.assets/image-20210127204219324.png)

	![image-20210127204247710](第二章-ElasticSearch安装以及简单使用.assets/image-20210127204247710.png)



6. 安装kibana可视化工具

	```bash
	# 这里的192.168.124.3切换为自己宿主机的IP地址。
	docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.124.3:9200 -p 5601:5601 -d kibana:7.4.2
	```

	

7. 访问 192.168.124.3:5610

	![image-20210127232614502](第二章-ElasticSearch安装以及简单使用.assets/image-20210127232614502.png)


​	



## 初步检索

### 1、_cat

* GET /_cat/nodes  ：查看所有节点

* GET /_cat/health  ：查看es健康状况 

* GET /_cat/master ：查看主节点

* GET /_cat/indices ：查看所有索引(数据库

	)![image-20210127205845415](第二章-ElasticSearch安装以及简单使用.assets/image-20210127205845415.png)![image-20210127205930107](第二章-ElasticSearch安装以及简单使用.assets/image-20210127205930107.png)![image-20210127211916628](第二章-ElasticSearch安装以及简单使用.assets/image-20210127211916628.png)



### 2、创建一个索引(数据库)

```bash
# 索引名称不能有大写
PUT http://ip地址:9200/索引名称
```

![image-20210127211714168](第二章-ElasticSearch安装以及简单使用.assets/image-20210127211714168.png)





### 3、写入文档(表中数据)

```bash
#如果ID号没有则随机生成一个
PUT|POST http://ip地址:9200/索引名称/类型名称/ID号
```

* 写入带ID号的数据

![image-20210127213059751](第二章-ElasticSearch安装以及简单使用.assets/image-20210127213059751.png)



> * 带ID添加数据且没有同ID数据为新增，否则为更新
> * 不带ID添加数据随机生成ID，为新增
> * PUT和POST均可在类型中添加数据，但是PUT必须带ID否则报错，而POST可以不带



### 4、查询文档

```bash
GET http://ip地址:9200/索引名称/类型名称/ID号
```

* 查询到

	![image-20210127214117781](第二章-ElasticSearch安装以及简单使用.assets/image-20210127214117781.png)

	> 可能是我ES版本太低了，这里字段应该还包括
	>
	> 1. _seq_no:并发控制字段，用来做乐观锁
	> 2. _primary_term : 同上，主分片重新分配，如重启就会变化

* 没有查询到

	![image-20210127214256572](第二章-ElasticSearch安装以及简单使用.assets/image-20210127214256572.png)



### 5、更新文档

* 方式一：带update

	```bash
	POST http://ip地址:9200/索引名称/类型名称/ID号/_update
	{
		"doc":{
			"name":"修改数据"
		}
	}
	```

	* 第一次修改

		![image-20210127220418074](第二章-ElasticSearch安装以及简单使用.assets/image-20210127220418074.png)

	* 第二次修改

		![image-20210127220618829](第二章-ElasticSearch安装以及简单使用.assets/image-20210127220618829.png)

	> 即多次更新如果源数据没有发生变化则version不会变化，且result变为noop

* 方式二

	```bash
	POST http://ip地址:9200/索引名称/类型名称/ID号
	{
		"doc":"修改数据"
	}
	```

	> 多次修改后 _version  每次都增加  而result始终都是update  。即执行多少次则更新多少次

* 方式四

	```bash
	PUT http://ip地址:9200/索引名称/类型名称/ID号/_update
	{
		"doc":{
			"name":"修改数据"
		}
	}
	或者
	PUT http://ip地址:9200/索引名称/类型名称/ID号
	{
		"doc":"修改数据"
	}
	```



#### 新增属性

```bash
PUT http://ip地址:9200/索引名称/类型名称/ID号/_update
{
	"doc":{
		"name":"修改数据",
		"age":45
	}
}
或者
PUT http://ip地址:9200/索引名称/类型名称/ID号
{
	"doc":"修改数据",
	"age":45
}
```

> 将PUT换成POST	效果一样

#### 乐观锁

> 利用上面讲道的两个字段 ==_seq_no==和==_primary_term== ，数据有修改时就会变化。

更新数据是携带  ==?if_seq_no=1&if_primary_term=1==   

```bash
#这里的两个 1 应该为查出来的值
PUT http://ip地址:9200/索引名称/类型名称/ID号?if_seq_no=1&if_primary_term=1
{
	"doc":"修改数据",
	"age":45
}
```



### 6、删除文档&索引

```bash
#删除文档
DELETE http://ip地址:9200/索引名称/类型名称/ID号
#删除索引
DELETE http://ip地址:9200/索引名称
```



### 7、bulk批量API

>   使用Kibina，postman软件换行有问题。

`格式:`

```bash
# 两行为一个操作内容  第一行：操作行为     第二行：请求内容
{action:{metadata}}\n
{requstbody}\n (请求体)
```

`action` : 必须是以下4种选项之一

-   `index`(最常用) : 如果文档不存在就创建他，如果文档存在就更新他
-   `create` : 如果文档不存在就创建他，但如果文档存在就返回错误
    -   使用时一定要在metadata设置`_id`值，他才能去判断这个文档是否存在
-   `update` : 更新一个文档，如果文档不存在就返回错误
    -   使用时也要给`_id`值，且后面文档的格式和其他人不一样
-   `delete` : 删除一个文档，如果要删除的文档id不存在，就返回错误
    -   使用时也必须在metadata中设置文档`_id`，且后面不能带一个doc，因为没意义，他是用`_id`去删除文档的





*   批量添加

    ```bash
    POST /索引名称/类型名称/_bulk
    #第一个操作
    {"index":{"_id":1}}  \\行为：索引信息
    {"title":"Java","price","55"} \\请求体
    #第二个操作
    {"index":{"_id":2}}
    {"title":"Html5","price","45"}
    #第三个操作
    {"index":{"_id":3}}
    {"title":"Php","price","35"}`
    #第四个操作
    {"index":{"_id":4}}
    {"title":"Python","price","50"}
    ```

![image-20210128165850101](第二章-ElasticSearch安装以及简单使用.assets/image-20210128165850101.png)



*   批量查询

    ```bash
    # 可以查询不同的索引以及不同的类型还可以查询指定字段
    GET _mget
    {
      "docs":[
         {
            "_index": "testindex",
            "_type": "testType",
            "_id": "1",
            "_source": "title"
         },
         {
            "_index": "testindex",
            "_type": "testType",
            "_id": "2",
            "_source": ["price","title"]
         }
      ]
    }
    
    GET /索引名称/类型名称/_mget
    {
      "docs":[
         {
            "_id": "1"
         },
         {
            "_id": "2"
         }
      ]
    }
    
    GET /索引名称/类型名称/_mget
    {
      "ids":["1","2"]
    }
    ```

![image-20210128175626520](第二章-ElasticSearch安装以及简单使用.assets/image-20210128175626520.png)

*   其余批量操作

    ```bash
    POST 127.0.0.1/mytest/doc/_bulk
    //创建一笔数据
    { "create" : { "_id": 1 } }
    { "color": "create black" }
    //创建一笔数据，因为id=1的文档已经存在，所以会创建失败
    { "create" : { "_id": 1 } }
    { "color": "create black2" }
    //索引一笔数据
    { "index" : { "_id": 2 } } 
    { "color": "index red" }
    //索引一笔数据，但是index可以创建也可以更新，所以执行成功
    { "index" : { "_id": 2 } } 
    { "color": "index red2" }
    //索引一笔数据，不一定要设置id(index又能创建又能更新又不用设id，超好用)
    { "index": {} } 
    { "color": "index blue" } 
    //删除一笔文档，注意delete后面不接一个doc
    { "delete" : { "_id": "2" } } 
    //找不到此id的文档，删除失败
    { "delete" : { "_id": "2" } } 
    //更新一笔文档，注意doc格式不太一样
    { "update" : { "_id": 1 } } 
    { "doc": { "color": "update green"} }
    //更新一笔文档，但因为此id的文档不存在，所以更新失败
    { "update" : { "_id": 100 } } 
    { "doc": { "color": "update green2"} }
    ```

    