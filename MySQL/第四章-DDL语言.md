## DDL（Data Definition Language）

>   主要负责库表定义操作，比如库表的crud



### 库的管理

#### 1、库创建 - create

```mysql
create DATABASE [if not exists] 库名;
```



#### 2、库修改 - rename

```mysql
RENAME DATABASE books TO 新库名;
```



#### 3、更改库的字符集

```mysql
ALTER DATABASE books CHARACTER SET gbk;
```



#### 4、库的删除 - drop

```mysql
DROP DATABASE IF EXISTS 库名;
```

### 表的修改

#### 1、创建 - create

```mysql
create table 表名(
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	...
	列名 列的类型【(长度) 约束】
)
```

`案例`

```mysql
#案例：创建表Book
CREATE TABLE book(
	id INT,#编号
	bName VARCHAR(20),#图书名
	price DOUBLE,#价格
	authorId  INT,#作者编号
	publishDate DATETIME#出版日期
);
```



#### 2、修改 - alter

```mysql
#①修改列名
ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 新列类型;

#②修改列的类型或约束
ALTER TABLE 表名 MODIFY COLUMN 列名 新列类型;

#③添加新列
ALTER TABLE 表名 ADD COLUMN 新列名 新列类型; 

#④删除列
ALTER TABLE 表名 DROP COLUMN  列名;

#⑤修改表名
ALTER TABLE 表名 RENAME TO 新表名;
```



#### 4、删除 - drop

```mysql
DROP TABLE IF EXISTS book_author;
```



#### 5、表的复制

* 复制表结构

	```mysql
	CREATE TABLE copy LIKE 表名;
	```

* 复制表结构和数据

	```mysql
	CREATE TABLE copy2 
	SELECT * FROM 表名;
	```

* 只复制部分数据

	```mysql
	CREATE TABLE copy3
	SELECT id,au_name
	FROM 表名 
	WHERE 条件;
	```

* 仅仅复制某些字段

	```mysql
	CREATE TABLE copy4 
	SELECT id,au_name
	FROM 表名
	WHERE 0;
	```


