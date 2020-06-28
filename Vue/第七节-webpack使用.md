### 什么是webpack

从本质上来讲，webpack是一个现代的JavaScript应用的静态模块打包工具。其主要有两个功能    `模块`  `打包`

第六节中讲到ES6中JavaScript模块化开发。如果页面依赖许多的模块js文件，则需要一次引入并且还需要import各个模块。甚是麻烦。


<font color=ff00aa>`模块`</font>

    webpack可以帮助我们进行模块化，并且处理模块间的各种依赖关系。


<font color=ff00aa>`打包`</font>

    webpa的模块和打包功能将整个项目的js模块文件均打包到一个或多个js文件中，因此在项目页面中只需要引入有限js文件即可。并且在打包的过程中，还可以对资源进行处理，比如压缩图片，将scss转成css，将ES6语法转成ES5语法，将TypeScript转成JavaScript等等操作。


### 同类技术grunt/gulp

grunt/gulp的核心是Task我们可以配置一系列的task，并且定义task要处理的事务（如ES6、ts转化，图片压缩，scss转成css）之后让grunt/gulp来依次执行这些task，而且让整个流程自动化。所以grunt/gulp也被称为前端自动化任务管理工具。

> 什么时候用grunt/gulp呢？

如果你的工程模块依赖非常简单，甚至是没有用到模块化的概念。
只需要进行简单的合并、压缩，就使用grunt/gulp即可。但是如果整个项目使用了模块化管理，而且相互依赖非常强，我们就可以使用更加强大的webpack了。

> grunt/gulp和webpack有什么不同呢？

grunt/gulp更加强调的是前端流程的自动化，模块化不是它的核心。webpack更加强调模块化开发管理，而文件压缩合并、预处理等功能，是他附带的功能。


### 初步使用webpack

#### 第一步：定义多个模块js文件

```JavaScript
info.js

export const name = 'why';
export const age = 18;
export const height = 1.88;
```

```JavaScript
mathUtils.js

function add(num1, num2) {
  return num1 + num2
}

function mul(num1, num2) {
  return num1 * num2
}

module.exports = {
  add,
  mul
}

```

#### 第二步：在main.js中引入需要的模块js文件

这里的main.js可以理解为一个页面或一个功能模块依赖的js模块文件。

譬如一个项目中有 ``我的`` ``首页``  `系统管理`三个页面|模块。而 `首页` 页面中依赖5个js文件，则在index.js中import这5个js文件。然后在 `首页` 页面中导入一个index.js即可。其他的  `我的` `系统管理`页面等类似。

```JavaScript
main.js

// 1.使用commonjs的模块化规范
const {add, mul} = require('./mathUtils.js')

console.log(add(20, 30));
console.log(mul(20, 30));

// 2.使用ES6的模块化的规范
import {name, age, height} from "./info";

console.log(name);
console.log(age);
console.log(height);
```

#### 第三步：使用bundle.js
在相关页面中引入即可。

    <script src="./dist/bundle.js"></script>’


### webpack-loader

在我们之前的实例中，我们主要是用webpack来处理我们写的js代码，并且webpack会自动处理js之间相关的依赖。但是，在开发中我们不仅仅有基本的js代码处理，我们也需要加载css、图片，也包括一些高级的将ES6转成ES5代码，将TypeScript转成ES5代码，将scss、less转成css，将.jsx、.vue文件转成js文件等等。
对于webpack本身的能力来说，对于这些转化是不支持的。
那怎么办呢？给webpack扩展对应的loader就可以啦。

loader使用过程：
步骤一：通过npm安装需要使用的loader
步骤二：在webpack.config.js中的modules关键字下进行配置
大部分loader我们都可以在webpack的官网中找到。。

具体内容可在资料中ppt观看以及相关代码
