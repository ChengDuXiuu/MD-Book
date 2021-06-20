## 什么是vue生命周期

在vue项目中，每个组件页面都是一个个vue实例，类似于bean的生命周期一样，描述了物体从诞生到销毁的过程



## 什么是vue生命周期钩子函数

即 vue实例在生命周期中特殊的时间段，作者在这些时间段提供的一些接口，如果我们实现了或者在这些钩子函数中执行了操作，那么这些操作就会应用到对应vue实例声明周期中特殊的时间段



## 有哪些生命周期

* beforeCreate     ： 创建前

	> vue实例执行new操作，其他的什么都没做

* created             ： 创建后

	> 属性和方法都挂载到了vue实例上面

* beforeMount      ： 挂载前

	> 找到了el或者mount对一个的节点范围，即vue语法等都没有替换为html，vue变量以及el表达式还是变量或者表达式

* mounted           ： 挂载后

	> vue语法全部翻译为html，其中变量值也固定，事件例如@click都变为了js操作

* beforeUpdate    ： 数据更新前

* updated            ： 数据更新后

* beforeDestory    ：销毁前

* destroyed          ： 销毁后 