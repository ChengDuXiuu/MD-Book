1. Ll在components目录中新增vue文件

	```vue
	<template>
	  <div class="hello">
	    <h1>hello vue {{name}}</h1>
	    <h2>Essential Links</h2>
	    <el-row>
	      <el-button plain>朴素按钮</el-button>
	      <el-button type="primary" plain>主要按钮</el-button>
	      <el-button type="success" plain>成功按钮</el-button>
	      <el-button type="info" plain>信息按钮</el-button>
	      <el-button type="warning" plain>警告按钮</el-button>
	      <el-button type="danger" plain>危险按钮</el-button>
	    </el-row>
	  </div>
	</template>
	
	<script>
	export default {
	  name: 'hello',
	  data () {
	    return {
	      name: 'Welcome to Your Vue.js App'
	    }
	  }
	}
	</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
	<style scoped>
	</style>
	
	```

2. 在router/index.js中新增路由映射

	```javascript
	import Vue from 'vue'
	import Router from 'vue-router'
	import HelloWorld from '@/components/HelloWorld'
	import hello from '@/components/hello'
	import MyTable from '@/components/MyTable';
	
	Vue.use(Router)
	
	export default new Router({
	  routes: [
	    {
	      path: '/',
	      name: 'HelloWorld',
	      component: HelloWorld
	    },
	    {
	      path: '/hello',
	      name: 'hello',
	      component: hello
	    },
	    {
	      path: '/table',
	      name: 'MyTable',
	      component: MyTable
	    }
	  ]
	})
	```

	

	