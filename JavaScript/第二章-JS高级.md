## JavaScript特点

* 解释执行

  > 解释一行执行一行。需要多次翻译因此，效率低于编译执行

* 灵活、动态特性

  > 可以随意给对象增加属性和方法

* 头等函数

  > 函数功能特别强大，函数返回函数以及自调用等等。

* 执行环境

  > 1. 浏览器
  > 2. NodeJS



## JavaScript组成

* ECMAScript

  > 语法规范。变量、数据类型、操作符、内置对象等等

* Web APIs

  * BOM

    > 操作浏览器事件以及行为

    1. onload页面加载事件、window顶级对象。

    2. 定时器

    3. location、history

  * DOM

    > 文档对象模型，操作页面。

    1. 获取页面元素、注册事件
    2. 属性操作、样式操作
    3. 节点属性、节点层级
    4. 动态创建元素
    5. 事件：注册事件的方式、事件的三个阶段(捕获，目标，冒泡)、事件对象



## 浏览器如何工作

 ![img](https://img-blog.csdn.net/20140211165714437) 

* 用户界面(User Interface)：除了显示请求页面用的浏览器主窗口外，其他部分都属于用户界面，包括地址栏、前进/后退按钮、书签菜单等。

* 浏览器引擎(Browser engine)：查询和操作渲染引擎的接口。

* 渲染引擎(Rendering engine)：将数据(HTML、css等)渲染成DOM树并存储在内存中。然后在屏幕上显示。

*  网络(Networking)：网络调用，例如HTTP请求。

* JavaScript解释器(JavaScriptInterpreter)：JavaScript引擎，专门处理`内存中DOM`个别情况会处理BOM。主要操作内存中DOM树，当操作完成后，渲染引擎会根据`内存中DOM`重新渲染。

* 用户界面后端(UI Backend)：绘制基本的窗口小部件，比如组合框和窗口。

* 数据存储(Data Persistence)：数据持久层。浏览器在硬盘上存储数据，比如cookie。HTML5定义了网络数据库。

## 面向对象编程

> 干一件事情：打印学生的信息

* 面向过程

	```javascript
	//面向过程
	let stu1={name:'stu1',age:'15',score:'98'};
	let stu2={name:'stu1',age:'15',score:'98'};
	console.log(stu1.name,stu1.age,stu1.score);
	console.log(stu2.name,stu2.age,stu2.score);
	```

* 面向对象

	```javascript
	/面向对象
	//1. 找到对象 ：学生       学生属性有name、age、score。方法有打印自己信息
	//2. 创建一个模板（实例instance）在JavaScript中创建对象的模板是构造函数。其他语言中是类
	function student(name,age,score) {
	    this.name=name;
	    this.age=age;
	    this.score=score;
	
	    this.printInfo=function () {
	        console.log(this.name,this.age,this.score);
	    }
	}
	//3. 构建实例
	let st1=new student("stu1",15,'89');
	let st2=new student("stu2",15,'89');
	
	st1.printInfo();
	st2.printInfo();
	```

	![image-20201222225842093](第二章-JS高级.assets/image-20201222225842093.png)



### 创建对象方式

```javascript
//todo 创建对象方式
// 1. new Object() -- 只能创建单个对象
let stu=new Object();//空对象
stu.name="sut1";
stu.age=48;
stu.score=89;

stu.printInfo=function () {
    console.log(this.age,this.name,this.score);
}
stu.printInfo();

// 2. 字面量   -- 只能创建单个对象
let teacher={
    name:"tea1",
    age:48,
    score:98,
    printInfo:function () {
        console.log(this.name,this.age,this.score);
    }
}
teacher.printInfo();

//todo 如何对象模板呢
// 3. 对象工厂函数  -- 无法获取对象具体类型
function factory(name,age,score){
    let obj=new Object();
    obj.name=name;
    obj.age=age;
    obj.score=score;

    obj.printInfo=function () {
        console.log(this.name,this.age,this.score);
    }
    return obj;
}
let obj1=factory("obj1",15,89);
let obj2=factory("obj2",145,19);
obj1.printInfo();
obj2.printInfo();


//todo 如何创建具体类型的对象并且能够多次实例
// 4. 构造函数  -- 强烈安利
function father(name,age,score) {
    this.name=name;
    this.age=age;
    this.score=score;

    this.printInfo=function () {
        console.log(this.name,this.age,this.score);
    }
}

let fea1=new father("fea1",45,78);
let fea2=new father("fea2",45,78);

fea1.printInfo();
fea2.printInfo();
if (fea1 instanceof father){
    console.log("fea1是对象teacher实例！！");
}
```

![image-20201222231846987](第二章-JS高级.assets/image-20201222231846987.png)



### 静态成员和实例成员

> 先搞清楚一个概念，上面我们说了创建对象的四种方式，第四种构造函数的方式才能够创建这个对象father的实例fea1和fea2类似于Java中的new，其余三种都是直接创建了实例。

* 静态成员：直接使用对象来调用的

	> 例如上面讲的 ==字面量==创建的对象teacher。其属性name、age、score和方法printInfo都是静态成员。`常用于工具类，例如Math`

* 实例成员：构造函数中的成员就是实例成员

	> 例如上面讲的==构造函数==创建的对象，必须实例后才能调用其成员。例如对象father必须实例fea1和fea2后通过实例进行访问的成员叫实例成员。`常用于很多实例的情况`



### 构造函数中方法冗余问题

> 例如上面讲到构造函数创建了一个对象father。并且实例了两个对象fea1和fea2，fea1中属性开辟内存空间存放而fea1中方法重新开辟另一个空间存放printInfo。就好比Java对象一样，在==栈内存存放对象名==，==对象实例存放在堆内存==。并且fea2中的printInfo方法会再次开辟新的空间存放。都会导致相同的多个printInfo冗余。

* 解决printInfo存储多份问题

	```javascript
	// todo 解决printInfo存储多份问题
	function mom(name,age,score) {
	    this.name=name;
	    this.age=age;
	    this.score=score;
	
	    this.printInfo=printInfo;
	}
	
	// 将函数变为全局函数
	function printInfo() {
	    console.log(this.name,this.age,this.score);
	}
	
	let mom1=new mom("mom1",45,78);
	let mom2=new mom("mom2",45,78);
	
	mom1.printInfo();
	mom2.printInfo();
	
	console.log(mom1.printInfo==mom2.printInfo);
	```

​		![image-20201222234607134](第二章-JS高级.assets/image-20201222234607134.png)



`缺陷：`

​		如果对象中有n个函数，你就得将n个函数变为全局函数。

`解决方法：`

​		原型



### 原型

> Javascript规定，每一个函数都有一个prototype对象属性，指向另一个对象（原型链上面的）

```javascript
//todo 原型 解决n个全局函数问题
// 每一个构造函数都有一个属性  ：  原型/原型对象
function proto(name,age) {
    this.name=name;
    this.age=age;
}

proto.prototype.printInfo=function () {
    console.log(this.name,this.age);
}

let pro1=new proto('pro1',48);
let pro2=new proto('pro2',56);

pro1.printInfo();
pro2.printInfo();

console.log(pro1.printInfo==pro2.printInfo);
```

![image-20201222235454116](第二章-JS高级.assets/image-20201222235454116.png)



### 阶段总结

> 创建对象的方法有多重，工具对象用==字面量==。其他用==构造函数==，如果对象中有函数则使用==原型==来指向。

