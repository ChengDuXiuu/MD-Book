## ES6新特性

1. const和let

	> 常量和局部变量
	>
	> - 常量 const 声明时，请使用大写变量，如：CAPITAL_CASING
	> - const 在声明时必须被赋值

2. 模板字符串

	> console.log(`读取到数据 --- ${*data*}`);

3. 箭头函数

	> fs.readFile('./file/data.txt', { encoding: 'utf-8' }, (*err*, *data*) => {
	>
	> ​            if (*err*) throw *err*;
	>
	> ​            console.log(`读取到数据 --- ${*data*}`);
	>
	> ​        });

4. 参数默认值

	```javascript
	// ES6之前，当未传入参数时，text = 'default'；
	function printText(text) {
	    text = text || 'default';
	    console.log(text);
	}
	
	// ES6；
	function printText(text = 'default') {
	    console.log(text);
	}
	
	printText('hello'); // hello
	printText();// default
	```

5. 二进制和八进制字面量

	```jsx
	let oValue = 0o10;
	console.log(oValue); // 8
	 
	let bValue = 0b10; // 二进制使用 `0b` 或者 `0B`
	console.log(bValue); // 2
	```

6. 对象和数组解构

	```jsx
	// 对象
	const student = {
	    name: 'Sam',
	    age: 22,
	    sex: '男'
	}
	// 数组
	// const student = ['Sam', 22, '男'];
	
	// ES5；
	const name = student.name;
	const age = student.age;
	const sex = student.sex;
	console.log(name + ' --- ' + age + ' --- ' + sex);
	
	// ES6
	const { name, age, sex } = student;
	console.log(name + ' --- ' + age + ' --- ' + sex);
	```

7. 对象超类

	```jsx
	var parent = {
	  foo() {
	    console.log("Hello from the Parent");
	  }
	}
	 
	var child = {
	  foo() {
	    super.foo();
	    console.log("Hello from the Child");
	  }
	}
	 
	Object.setPrototypeOf(child, parent);
	child.foo(); // Hello from the Parent
	             // Hello from the Child
	```

8. for...of 和 for...in

	* for...of 用于遍历一个迭代器，如数组：

		```javascript
	let letters = ['a', 'b', 'c'];
		letters.size = 3;
		for (let letter of letters) {
		  console.log(letter);
		}
		// 结果: a, b, c
		```
	
	* for...in 用来遍历对象中的属性：

		```jsx
	 let stus = ["Sam", "22", "男"];
		 for (let stu in stus) {
	   console.log(stus[stu]);
		  }
		// 结果: Sam, 22, 男
		```
	
		

9. 属性和方法简写

	* 属性

	* 方法

		```javascript
		let url = 'http://kylebing.cn/dontstarve'
		$.ajax({
		    url: url,
		    success: function (data) {
		        console.log(data)
		    }
		})
		等同于
		let url = 'http://kylebing.cn/dontstarve'
		$.ajax({
		    url,    // 属性名和变量为相同时，直接写一个，跟 url: url 相同
		    
		    success(data){   // 匿名方法的时候，可以直接写成这样。跟 success: function(data){} 相同。
		        console.log(data)
		    }
		})
		
		```

		