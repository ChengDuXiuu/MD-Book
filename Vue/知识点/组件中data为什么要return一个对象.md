## vue项目中new Vue中 data和 普通组件中data为什么不一样

* new Vue中data

	```vue
	new Vue({
	  el: '#app',
	  data:{
	    msg:'a'
	  },
	  router,
	  store
	})
	```

* 普通组件中data

	``` vue
	data () {
	    return {
	      ue: null,
	      ueId: `J_ueditorBox_${new Date().getTime()}`,
	      ueContent: '',
	      dialogVisible: false
	    }
	},
	```

> 为什么new Vue中data直接是一个json对象，而普通组件中data需要return一个对象？

* 因为js的设计就是这样的，当js对象在==原型==上添加一个==属性并且为对象==时，则js对象==多个实例共享一个原型属性上对象值==

	```javascript
	function father(name) {
	    //属性直接复制值没有问题
	    this.name=name;
	    //属性直接赋值对象没有问题
	    this.obj={
	        a:'aa'
	    }
	    //原型上添加属性是值没有问题
	    father.prototype.ele='ele'
	    
	    //原型上添加属性是一个对象则有问题
	    father.prototype.data={
	        a:'aa'
	    }
	}
	
	let fea1=new father("fea1");
	let fea2=new father("fea2");
	console.log(fea1.name)
	console.log(fea2.name)
	console.log(fea1.obj.a)
	console.log(fea2.obj.a)
	console.log(fea1.data.a)
	console.log(fea2.data.a)
	console.log(fea1.ele)
	console.log(fea2.ele)
	fea2.name='new name'
	fea2.obj.a='new obj a'
	fea2.data.a='new data a'
	fea2.ele='new ele'
	console.log(fea1.name)
	console.log(fea2.name)
	console.log(fea1.obj.a)
	console.log(fea2.obj.a)
	console.log(fea1.data.a)
	console.log(fea2.data.a)
	console.log(fea1.ele)
	console.log(fea2.ele)
	
	//结果
	fea1
	fea2
	aa
	aa
	aa
	aa
	ele
	ele
	-----
	fea1
	new name
	aa
	new obj a
	new data a
	new data a
	ele
	new ele
	```

* 又因为 new Vue() 属于根组件 整个vue项目中只存在一个，因此不存在多实例相互影响问题，而普通组件存在复用的问题，因此要使用return 一个对象，每次return都是一个新的对象

