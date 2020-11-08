## 事务的作用

保证数据入库的高可用。



> 单表入库时，数据校验就是入库最后一道保障。而如果一个方法中操作了多个表A、B、C 在B和C之间的代码抛出异常则A/B入库成功，C则入库失败，我们在一个方法中同时操作三张表，相比这三张表需要保证原子性。要么都成功，有一个失败则全不会滚。



## 开启事务

```java
@Transactional(rollbackFor = Exception.class)
```

> 在需要开启的事务的方法上使用该注解即可(包括controller类)



> <font color=ff00aa>注意：</font> 事务回滚只针对显式抛出异常有效(throw new RuntimeException("手动抛出异常");)，如果try catch 捕获了异常则无效

