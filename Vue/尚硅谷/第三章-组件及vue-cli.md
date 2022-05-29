# 组件

## 非单文件组件

所有的vue组件放在同一个文件中，开发中不推荐。







## 单文件组件

每一个模块对应一个组件

* 组件一

	```vue
	<template>
		<div class="demo">
			<h2>学校名称：{{name}}</h2>
			<h2>学校地址：{{address}}</h2>
			<button @click="showName">点我提示学校名</button>	
		</div>
	</template>
	
	<script>
	  //默认导出
		 export default {
	     //组件名
			name:'School',
	     //剩下的和vue基础中一模一样
			data(){
				return {
					name:'尚硅谷',
					address:'北京昌平'
				}
			},
			methods: {
				showName(){
					alert(this.name)
				}
			},
		}
	</script>
	
	<style>
		.demo{
			background-color: orange;
		}
	</style>
	```

	

* 组件二

	```vue
	<template>
		<div>
			<h2>学生姓名：{{name}}</h2>
			<h2>学生年龄：{{age}}</h2>
		</div>
	</template>
	
	<script>
		 export default {
			name:'Student',
			data(){
				return {
					name:'张三',
					age:18
				}
			}
		}
	</script>
	```

	

* 汇总组件(main函数)

	```vue
	<template>
		<div>
			<School></School>
			<Student></Student>
		</div>
	</template>
	
	<script>
		//引入组件
		import School from './School.vue'
		import Student from './Student.vue'
	
		export default {
			name:'App',
			components:{
				School,
				Student
			}
		}
	</script>
	```

* 创建vue实例

	```js
	import App from './App.vue'
	
	new Vue({
		el:'#root',
		template:`<App></App>`,
		components:{App},
	})
	```

* 创建容器

	```html
	<!DOCTYPE html>
	<html>
		<head>
			<meta charset="UTF-8" />
			<title>练习一下单文件组件的语法</title>
		</head>
		<body>
			<!-- 准备一个容器 -->
			<div id="root"></div>
			<!-- <script type="text/javascript" src="../js/vue.js"></script> -->
			<!-- <script type="text/javascript" src="./main.js"></script> -->
		</body>
	</html>
	```

	