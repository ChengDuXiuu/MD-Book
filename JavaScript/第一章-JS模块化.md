## 作用

* 代码解耦、高可用、高维护  
* 代码复用、按需加载
* 降低代码复杂度

## 进化史

1. 所有的js代码写在一块

	> Global被污染，命名冲突，阅读性极差

2. 简单对象封装--命名空间模式/全局函数模式

	> 减少了Global上的变量数目，但由于是对象可以通过obj.属性操作，一点都不安全

	* 全局函数模式

		```javascript
		//module1.js
		// 全局函数模式、将不同的功能封装成不同的全局函数
		let msg="module1"
		
		function foo(){
		    console.log("foo()",msg);
		}
		
		function bar() {
		    console.log("bar()",msg);
		}
		```

		```html
		//test1.html
		<script type="text/javascript" src="module1.js"></script>
		<script>
		foo();
		bar();
		// 修改全局变量 msg 引发不安全问题
		msg="update";
		foo()
		bar()
		</script>
		```

		![image-20201219175242457](第一章-JS模块化.assets/image-20201219175242457.png)

	* 命名空间模式

		```javascript
		// module2.js
		// 命名空间模式、使用对象进行简单封装
		
		let obj={
		    msg:'module2',
		    foo(){
		        console.log("foo()",this.msg);
		    }
		}
		```

		```html
		<script type="text/javascript" src="module2.js"></script>
		<script>
		obj.foo();
		// 修改全局变量 msg 引发不安全问题
		obj.msg="update";
		obj.foo();
		</script>
		```

		![image-20201219180127010](第一章-JS模块化.assets/image-20201219180127010.png)

		

3. 匿名闭包：IIFE模式

	> 函数式JavaScript唯一的local scope 

	```javascript
	// IIFE、匿名函数自调用-闭包
	
	// (function () {
	//     let msg="module3";
	//     function foo() {
	//         console.log("foo()",msg);
	//     }
	// })()
	
	// 上面形式：外部访问不到里面的属性msg和函数foo
	(function (window) {
	    let msg="module3";
	    function foo() {
	        console.log("foo()",msg);
	    }
	    window.module3={foo:foo()}//给window对象挂载module3属性
	})(window)
	// 上面形式：访问可通过module3.foo();
	
	```

	```html
	<script type="text/javascript" src="module3.js"></script>
	<script>
	foo();//直接调用访问不到，window对象上并没有foo()函数
	module3.foo();
	</script>
	```

	

4. 在匿名闭包基础上引入依赖: 模块模式

	> 例如 Jquery就是将window全局对象引入操作后返回。

	```javascript
	// IIFE增强、引入依赖 这里使用jQuery
	
	(function (window,$) {
	    let msg="module3";
	    function foo() {
	        console.log("foo()",msg);
	    }
	    window.module4=foo; //module4属性直接指向函数foo
	    $('body').css('background','red');//修改页面背景色
	})(window,jQuery);
	
	
	```

	```html
	<script type="text/javascript" src="jQuery2.24.js"></script>
	<script type="text/javascript" src="module4.js"></script>
	<script>
	module4;
	</script>
	```

## 带来的问题

* js文件数据增多，导致请求次数骤增
* 依赖模糊，比如jQuery和第三方库
* 难以维护

> 如何解决上诉问题，引出模块化开发规范。



## 模块化规范

* CommonJS 

	> node在使用的一个规范

	`说明：`

	* 每个文件都可当做一个模块
	* 在服务器端：模块的加载时运行时同步加载的
	* 在浏览器端：模块需要提前编译打包处理、因为有很多语法浏览器不识别，需要转换成浏览器识别语法

	`基本语法`

	* 暴露模块

		1. module.exports = value

		2. exports.xxx = value

			> 暴露的本质：都是暴露exports

	* 引入模块

		1. 自定义模块

			require(xxx) xxx为==模块文件路径==

		2. 第三方模块

			require(xxx) xxx为==模块名==

	`实现`

	* 服务端实现者

		1. Node.js   http://nodejs.cn/

		2. 实现步骤

			* 创建文件

				![image-20201219190037655](第一章-JS模块化.assets/image-20201219190037655.png)

			* 在05-CommonJS-Node目录下使用npm安装uniq第三方模块

				```bash
				npm install uniq
				```

			* 编写文件

				```javascript
				//module1.js
				// modeule.exports = obj 暴露一个对象
				module.exports = {
				    msg: 'module1',
				    foo() {
				        console.log("foo()",this.msg);
				    }
				}
				//module2.js
				// modeule.exports = function(){} 暴露一个函数
				module.exports = function () {
				    console.log("function()",'module2');
				}
				//会覆盖上面的内容
				module.exports = function () {
				    console.log("function()",'module2-update');
				}
				//module3.js
				// exports.xxx = value
				exports.foo = function () {
				    console.log("f00()",'module3');
				}
				
				exports.bar = function () {
				    console.log("bar()",'module3');
				}
				```

				```javascript
				//app.js
				let module1=require('./modules/module1')
				let module2=require('./modules/module2')
				let module3=require('./modules/module3')
				
				//使用module1
				console.log(module1.msg);
				module1.foo();
				//使用module2
				module2();
				//使用module3
				module3.foo();
				module3.bar();
				```

			* 执行app.js

				```bash
				node app.js
				```

				![image-20201219191230046](第一章-JS模块化.assets/image-20201219191230046.png)

			* 使用第三方依赖uniq

				```javascript
				//module3.js
				// exports.xxx = value
				exports.foo = function () {
				    console.log("f00()",'module3');
				}
				
				exports.bar = function () {
				    console.log("bar()",'module3');
				}
				//新增变量
				exports.arr=['1','4','5','1','4','9','5'];
				```

				```javascript
				//app.js
				//引入第三方模块
				let uniq = require('uniq')
				let module1=require('./modules/module1')
				let module2=require('./modules/module2')
				let module3=require('./modules/module3')
				
				//使用module1
				console.log(module1.msg);
				module1.foo();
				//使用module2
				module2();
				//使用module3
				module3.foo();
				module3.bar();
				//使用第三方模块
				let result=uniq(module3.arr);
				console.log(result);
				```

				![image-20201219191641482](第一章-JS模块化.assets/image-20201219191641482.png)

	* 浏览器端使用

		1. Browserify 打包工具：http://browserify.org/（通常用于Node.js的）==commonjs模块打包为可以在浏览器中使用==。

		2. 实现步骤

			* 下载browserify 

				```bash
				npm install browserify --save-dev    //局部下载
				```

			* 给上面使用commonjs模块的05-CommonJS-Node工程打包js

				```bash
				node_modules/.bin/browserify app.js -o ./target/build.js
				```

				> 使用命令时需要加上browserify安装路径，否则提示无效命令。

				![image-20201219193012151](第一章-JS模块化.assets/image-20201219193012151.png)

		

* AMD   - - RequireJS

	`说明：`

	* 专门针对浏览器端，模块的加载都是异步的。

	`基本语法`

	* 暴露模块

		```javascript
		// 暴露没有依赖的模块
		define(function({
			return 模块
		}))
		//暴露有依赖的模块
		define(['module1','module2'],function(m1,m2){
		  return 模块
		})
		```

	* 引入模块

		```javascript
		require['module1','module2'],function(m1,m2){
		  使用m1/m2
		})
		```

	`实现`

	* 无规范(RequireJS)实现

		1. 新建如下文件

			![image-20201219200151376](第一章-JS模块化.assets/image-20201219200151376.png)

		2. 编写文件

			```javascript
			//dataService.js
			//定义一个没有依赖的模块
			(function (window) {
			    let name="dataService.js";
			    function getName() {
			        return name;
			    }
			    window.dataService={getName};
			})(window)
			
			//alerter.js
			//定义一个有依赖的模块:依赖dataService.js
			(function (window,dataService) {
			    let name="alerter.js";
			    function showMsg() {
			        console.log(name,"依赖>>" + dataService.getName())
			    }
			    window.alerter={showMsg};
			})(window,dataService)
			
			//app.js
			//主模块
			(function (alerter) {
			    alerter.showMsg();
			})(alerter)
			
			```

			```html
			<!DOCTYPE html>
			<html lang="en">
			<head>
			    <meta charset="UTF-8">
			    <title>Title</title>
			</head>
			<body>
			<!--注意先后顺序-->
			<script type="text/javascript" src="./JS/dataService.js"></script>
			<script type="text/javascript" src="./JS/alerter.js"></script>
			<script type="text/javascript" src="./app.js"></script>
			
			</body>
			</html>
			```

		3. 结果

			![image-20201219200353952](第一章-JS模块化.assets/image-20201219200353952.png)

	* 使用规范(RequireJS)实现

		1. 创建文件

			![image-20201219204939700](第一章-JS模块化.assets/image-20201219204939700.png)

		2. 编辑文件

			```javascript
			//require.js
			百度下载即可
			
			//jquery-3.5.1.js
			百度下载即可
			
			//dataService.js
			//定义一个没有依赖的模块
			define(function () {
			    let name='dataService.js';
			    function getName() {
			        return name;
			    }
			    //暴露模块
			    return {getName};
			})
			
			//alerter.js
			//定义一个有依赖的模块:依赖dataService.js和第三方模块jQuery
			//定义一个有依赖的模块:依赖dataService.js
			define(['dataService','jquery'],function (dataService,$) {
			    let msg='alerter.js';
			    function showMsg() {
			        console.log(msg,dataService.getName());
			    }
			    $('body').css('background','red');
			    //暴露模块
			    return {showMsg};
			})
			
			
			//main.js
			(function () {
			    requirejs.config({
			        // baseUrl: 'js/libs',//基本路径,省掉页面js模块先后引入
			        paths: { //当解析模块文件发现依赖模块则在该配置中寻找
			            dataService: './modules/dataService',
			            alerter: './modules/alerter',
			            jquery: './libs/jquery-3.5.1'//jquery遇到AMD规范必须使用小写jquery，因为jQuery默认支持AMD
			        }
			    })
			    requirejs(['alerter'],function (alerter) {
			        alerter.showMsg();
			    })
			})()
			
			```

			```html
			<!DOCTYPE html>
			<html lang="en">
			<head>
			    <meta charset="UTF-8">
			    <title>Title</title>
			</head>
			<body>
			<!--引入require.js 并指定js主文件的入口-->
			<script data-main="js/main.js" src="js/libs/require.js"></script>
			</body>
			</html>
			
			```

			

* CMD

	> 阿里云规范，可忽略

* ES6

	`说明：`

	* 依赖模块需要编译打包处理。
	* 应用最广泛

	`基本语法`

	* 暴露模块

		```javascript
		export
		```

	* 引入模块

		```javascript
		import
		```

	`实现`

	* 浏览器端

		1. 使用Babel将ES6编译为ES5代码(下面支持情况并不是完全百分之百支持！)

			| Browser | Version | Date     |
			| ------- | ------- | -------- |
			| Chrome  | 58      | Apr 2017 |
			| Firefox | 54      | Jun 2017 |
			| Edge    | 14      | Aug 2016 |
			| Safari  | 10      | Sep 2016 |
			| Opera   | 55      | Aug 2017 |

		2. 使用Browserify编译打包js

			

	`实现步骤`

	1. 创建文件

		![image-20201220010926745](第一章-JS模块化.assets/image-20201220010926745.png)

	2. 编辑文件

		```javascript
		//.babelrc  
		{
		  "presets": ["es2015"]
		}
		
		//package.json
		{
		  "name": "08-ES6-Babel-Browerify",
		  "version": "1.0.0",
		}
		
		//module1.js 
		// 暴露模块   分别暴露
		export function foo() {
		    console.log("foo() module1");
		}
		
		export function bar() {
		    console.log("foo() module1");
		}
		
		export let arr = [1,5,3,9];
		  
		//module2.js
		// 暴露模块   统一暴露
		function foo() {
		    console.log("foo() modul2");
		}
		
		function bar() {
		    console.log("foo() module2");
		}
		
		let arr = [1,5,3,9,6];
		
		export {foo,bar,arr};
		 
		  
		//module3.js
		  
		  
		
		//main.js
		//引入其他模块
		// import module1 from './module1';
		// import module2 from './module2';
		// console.log(module1,module2);
		
		//如果使用上面方式，则无法使用module1和module2中任何数据。必须使用解构赋值方式进行获取
		import {foo,bar,arr} from './module1';
		import {foo2,bar2,arr2} from './module2';
		
		foo();
		bar();
		console.log(arr);
		
		foo2();
		bar2();
		console.log(arr2);
		```

		```html
		//index.html
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>Title</title>
		</head>
		<body>
		<script type="text/javascript" src="./modules/main.js"></script>
		
		</body>
		  
		</html>
		```

		

	3. 安装babel-cli、babel-preset-es2015和browserify

		```bash
		npm install babel-cli browserify
		npm install babel-preset-es2015 --save-dev
		```

		

	4. 运行index.html

		> 浏览器提示：main.js:2 Uncaught SyntaxError: Cannot use import statement outside a module

	5. 使用Babel将ES6转ES5

		```bash
		node_modules/.bin/babel modules -d modules/build # 将modules下所有js都转化到modules/build目录下
		```

		![image-20201220012715248](第一章-JS模块化.assets/image-20201220012715248.png)

	6. 使用browserify将es5语法js文件打包为浏览器可识别js

		```bash
		node_modules/.bin/browserify modules/build/main.js -o ./modules/target/build.js
		```

	7. 修改index.html 为打包后的build.js

		```html
		<script type="text/javascript" src="./modules/target/build.js"></script>
		```

	8. 执行

		![image-20201220014045285](第一章-JS模块化.assets/image-20201220014045285.png)

	9. 使用module1和module2需要用到解构赋值方式，有没有其他方式更轻松方式获取模块中数据？

		* 编辑module3.js

			```javascript
			//暴露模块  默认暴露：暴露什么数据就收到什么数据并且只能暴露一次
			//语法 export default value;
			export default {
			    msg:'我是默认暴露',
			    foo(){
			        console.log(this.msg);
			    }
			}
			```

		* main.js新增

			```javascript
			import module3 from './module3';
			module3.foo();
			```

		* 重新==转化==、==编辑打包==结果如下:

			![image-20201220015531809](第一章-JS模块化.assets/image-20201220015531809.png)

			

	10. 如何使用第三方依赖呢？

		* 下载第三放模块 这里使用jQuery

			```bash
			npm install jquery@1.12.4
			```

		* 在main.js中新增

			```javascript
			import $ from 'jquery'
			$('body').css('background','green')
			```

		* 重新==转化==、==编辑打包==结果如下:

			![image-20201220020814141](第一章-JS模块化.assets/image-20201220020814141.png)

			