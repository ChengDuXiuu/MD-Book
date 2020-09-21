1. 如果实体类中关联了数据表。而如果想要再新增一个字段 ，但是不影响表。

	​	eg ：电子商品实体类中需要新增一个字段 List<dianzi> children 作为存放子分类(手机、电脑等数据)。而不用新增表字段。

	```java
	@TableField(exist = false)
	private List<CategoryEntity> children;
	```

