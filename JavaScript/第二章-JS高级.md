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

