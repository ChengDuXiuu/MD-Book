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

6. 对象和数组解构赋值

  ```jsx
  、、、、、、、、、解构
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
  
  、、、、、、、、赋值
  // 变量赋值
  let a = 1;
  let b = 2;
  let c = 3;
  ES6中等同于
  let [a, b, c] = [1, 2, 3];
  
  
  、、、、、、、、、字符串的解构赋值
  const [a, b, c, d, e] = 'hello';
  a // "h"
  b // "e"
  c // "l"
  d // "l"
  e // "o"
  数值、布尔值、函数等等。具体参考：https://es6.ruanyifeng.com/#docs/destructuring
  ```

7. 对象超类

	 Class 可以通过`extends`关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。  类通过`extends`关键字，继承了父类的所有属性和方法。 
	
	```jsx
	class ColorPoint extends Point {
	  constructor(x, y, color) {
	    super(x, y); // 调用父类的constructor(x, y)
	    this.color = color;
	  }
	
	  toString() {
	    return this.color + ' ' + super.toString(); // 调用父类的toString()
	  }
	}
	```
	
	* 与Java继承一样， 子类必须在`constructor`方法中调用`super`方法 
	
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

10. 类

    ES6之前生成实例对象的方法是构造函数，例如：

    ```javascript
    function Point(x, y) {
      this.x = x;
      this.y = y;
    }
    
    Point.prototype.toString = function () {
      return '(' + this.x + ', ' + this.y + ')';
    };
    
    var p = new Point(1, 2);
    ```

    使用类改写：

    ```javascript
    class Point {
      constructor(x, y) {
        this.x = x;
        this.y = y;
      }
    
      toString() {
        return '(' + this.x + ', ' + this.y + ')';
      }
    }
    const b = new Point();
    b.toString() 
    ```

* 类的构造函数与Java类似，若没有显示定义则默认生成一个空的构造函数。
* 与ES5保持一致，类的所有实例对象共享一个原型`对象`

