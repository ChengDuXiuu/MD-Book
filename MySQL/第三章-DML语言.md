## DML（Data Manipulate Language）语言

>   主要负责 数据 操作

### 插入语句

#### 1、第一种方式

`语法`

```mysql
insert into 表名(列名,...) values(值1,...);
```



`特点`

* 插入的值的类型要与列的类型一致或兼容
* 字段不可以为null的必须插入值(null)，字段可以为null的插入时可以不写
* 列的顺序可以调换
* 列数必须与值个数一致
* 可以省略列，默认所有列，而且列的顺序与表一致



#### 2、第二种方式

`语法`

```mysql
insert into 表名  set 列名=值,列名=值,...
```

`案例`

```mysql
INSERT INTO beauty
SET id=19,NAME='刘涛',phone='999';
```



#### 3、方式一二对比

* 方式一可以支持插入多行

	```mysql
	INSERT INTO beauty
	VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
	,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
	,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);
	```

* 方式一支持子查询

	> 将查询结果集插入到新行

	```mysql
	INSERT INTO beauty(id,NAME,phone)
	SELECT id,boyname,'1234567'
	FROM boys WHERE id<3;
	```
	



### 修改语句

`语法`

* 修改单表记录

	```mysql
	update 表名
	set 列=新值,列=新值,...
	where 筛选条件;
	```

	`案例`

	```mysql
	#案例1：修改beauty表中姓唐的女神的电话为13899888899
	UPDATE beauty SET phone = '13899888899'
	WHERE NAME LIKE '唐%';
	```

	

* 修改多表记录

	```mysql
	sql92语法：
	update 表1 别名,表2 别名
	set 列=值,...
	where 连接条件
	and 筛选条件;
	
	sql99语法：
	update 表1 别名
	inner|left|right join 表2 别名
	on 连接条件
	set 列=值,...
	where 筛选条件;
	```

	`案例`

	```mysql
	#案例 1：修改张无忌的女朋友的手机号为114
	UPDATE boys bo
	INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
	SET b.`phone`='119',bo.`userCP`=1000
	WHERE bo.`boyName`='张无忌';
	
	#案例2：修改没有男朋友的女神的男朋友编号都为2号
	UPDATE boys bo
	RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
	SET b.`boyfriend_id`=2
	WHERE bo.`id` IS NULL;
	```

	

### 删除语句

#### 1、第一种方式

`语法`

* 单表删除

	```mysql
	delete from 表名 where 筛选条件
	```

	`案例`

	```mysql
	#案例：删除手机号以9结尾的女神信息
	DELETE FROM beauty WHERE phone LIKE '%9';
	SELECT * FROM beauty;
	```

* 多表删除

	```mysql
	sql92语法：
	delete 表1的别名,表2的别名
	from 表1 别名,表2 别名
	where 连接条件
	and 筛选条件;
	
	sql99语法：
	delete 表1的别名,表2的别名
	from 表1 别名
	inner|left|right join 表2 别名 on 连接条件
	where 筛选条件;
	```

	> 如果两个表中记录都需要删除则delete后两个表都加，如果只是删除其中一个则delete后加删除的表即可

	`案例`

	```mysql
	#案例：删除张无忌的女朋友的信息
	DELETE b
	FROM beauty b
	INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
	WHERE bo.`boyName`='张无忌';
	
	#案例：删除黄晓明的信息以及他女朋友的信息
	DELETE b,bo
	FROM beauty b
	INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
	WHERE bo.`boyName`='黄晓明';

#### 2、第二种方式 （清空表数据）

`语法`

```mysql
truncate table 表名;
```



#### 3、delete和truncate区别

* delete 可以加where 条件，truncate不能加
* truncate删除，效率高一丢丢
* 假如要删除的表中有自增长列，如果用delete删除后，再插入数据，自增长列的值从断点开始，而truncate删除后，再插入数据，自增长列的值从1开始。
* truncate删除没有返回值，delete删除有返回值
* truncate删除不能回滚，delete删除可以回滚.
