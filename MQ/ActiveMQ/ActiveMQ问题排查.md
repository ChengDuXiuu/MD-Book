## 启动失败

1. 启动提示

	![image-20211128184927062](ActiveMQ问题排查.assets/image-20211128184927062.png)

2. 查看状态，并没有启动

	![image-20211128184950961](ActiveMQ问题排查.assets/image-20211128184950961.png)

3. 查看日志

	```bash
	./activemq console
	```

	提示如下报错

	![image-20211128185053445](ActiveMQ问题排查.assets/image-20211128185053445.png)

4. 查看配置文件 config/activemq.xml

	```xml
	:set number
	```

5. 搞不定，重新解压缩替换activemq.xml



