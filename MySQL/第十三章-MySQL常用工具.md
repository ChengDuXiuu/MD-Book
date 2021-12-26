# 常用工具

## MySQL-binlog

由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的文本格式，就会使用到mysqlbinlog 日志管理工具。



### 语法

```sql
mysqlbinlog [options]  log-files1 log-files2 ...

选项：
	
	-d, --database=name : 指定数据库名称，只列出指定的数据库相关操作。
	
	-o, --offset=# : 忽略掉日志中的前n行命令。
	
	-r,--result-file=name : 将输出的文本格式日志输出到指定文件。
	
	-s, --short-form : 显示简单格式， 省略掉一些信息。
	
	--start-datatime=date1  --stop-datetime=date2 : 指定日期间隔内的所有日志。
	
	--start-position=pos1 --stop-position=pos2 : 指定位置间隔内的所有日志。
```









## MySQL-dump

mysqldump 客户端工具用来备份数据库或在不同数据库之间进行数据迁移。备份内容包含创建表，及插入表的SQL语句。



### 语法

```sql
mysqldump [options] db_name [tables]

mysqldump [options] --database/-B db1 [db2 db3...]

mysqldump [options] --all-databases/-A
```



### 连接选项

```sql
参数 ： 
	-u, --user=name			指定用户名
	-p, --password[=name]	指定密码
	-h, --host=name			指定服务器IP或域名
	-P, --port=#			指定连接端口
```



### 输出内容选项

```sql
参数：
	--add-drop-database		在每个数据库创建语句前加上 Drop database 语句
	--add-drop-table		在每个表创建语句前加上 Drop table 语句 , 默认开启 ; 不开启 (--skip-add-drop-table)
	
	-n, --no-create-db		不包含数据库的创建语句
	-t, --no-create-info	不包含数据表的创建语句
	-d --no-data			不包含数据
	
	 -T, --tab=name			自动生成两个文件：一个.sql文件，创建表结构的语句；
	 						一个.txt文件，数据文件，相当于select into outfile  
```



### 示例

```sql
示例 ： 
  # 导出db01库中的tb_book表  到  tb_book.sql  
	mysqldump -uroot -p2143 db01 tb_book --add-drop-database --add-drop-table > tb_book.sql
	
	# 导出db01库中所有表  到  db01.sql
	mysqldump -uroot -p2143 db01 --add-drop-database --add-drop-table > db01.sql
	
	# 导出db01库中的tb_book 文件(.sql和.txt) 到/tmp目录下
	mysqldump -uroot -p2143 -T /tmp db01 tb_book
```





## MySQL-import/source

> 将 MySQL-dump导出的.sql和.txt 两个文件的数据导入库中。这两条指令都需要 进入到MySQL客户端进行操作



### 语法

```sql
mysqlimport [options]  db_name  textfile1  [textfile2...]
```



### 示例

* 导入SQL文件 和数据文件

	```sql
	mysqlimport -uroot -p2143 test /tmp/city.txt source /root/tb_book.sql
	```

* 导入数据文件

	```sql
	mysqlimport -uroot -p2143 test /tmp/city.txt
	```



## mysqlshow

mysqlshow 客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索引。



### 语法

```sql
mysqlshow [options] [db_name [table_name [col_name]]]

--count		显示数据库及表的统计信息（数据库，表 均可以不指定）

-i			显示指定数据库或者指定表的状态信息
```



### 示例

```sql
#查询每个数据库的表的数量及表中记录的数量
mysqlshow -uroot -p2143 --count

#查询test库中每个表中的字段书，及行数
mysqlshow -uroot -p2143 test --count

#查询test库中book表的详细情况
mysqlshow -uroot -p2143 test book --count
```

