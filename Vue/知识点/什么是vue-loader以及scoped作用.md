## vue loader作用

组件的定义有如下三种

1. 全局组件 

	使用Vue.component(‘组件名称’,组件详情对象)创建的组件

2. 局部组件

	new Vue({

	​	...

	​	components:{

	​		组件名称 : 组件详情对象

	​	}

	})

3. 文件组件

	以.vue格式的文件，一般出现在vue项目中

> 因为vue项目终归还是要奔跑在浏览器中的，而浏览器又不识别.vue文件，怎么执行呢？vue loader把.vue文件编译为html、css、js文件然后交由浏览器执行



## scoped作用

* 被scoped修饰的style样式标签只会在当前vue文件中生效，不会渗透到父组件和子组件等其他组件
* 没有被scoped修饰的style样式标签全局生效
* 全局style标签和局部style标签可以混用

