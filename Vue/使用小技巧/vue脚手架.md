## 方式一

1. 创建项目 vue-cli4

	```bash
	vue create 项目名称   
	```

	按需引入插件:例如vuex 、router等等

	![image-20200809165840355](vue脚手架.assets/image-20200809165840355.png)

2. 引入element-ui插件

	* <img src="../../../../../mac/Respository/MD-Book/博客项目研发/第一章-环境搭建.assets/image-20200810210251628.png" alt="image-20200810210251628" style="zoom:50%;"/>

	* <img src="../../../../../mac/Respository/MD-Book/博客项目研发/第一章-环境搭建.assets/image-20200810210251628.png" alt="image-20200810210251628" style="zoom:50%;" />

	* <img src="../../../../../mac/Respository/MD-Book/博客项目研发/第一章-环境搭建.assets/image-20200810210251628.png" alt="image-20200810210251628" style="zoom:50%;" />

		* ![image-20200809214104647](vue脚手架.assets/image-20200809214104647.png)

	 *  ![image-20200809214206205](vue脚手架.assets/image-20200809214206205.png)

		

3. 关联远程Github

	```bash
	Git init  
		git remote add gitee git@gitee.com:gadeGG/vue-shop.git
		git add | git commit | git push
	```

4. 如若报错 参考 ：https://www.liaoxuefeng.com/wiki/896043488029600/1163625339727712

5. 或者是先新建GitHub仓库然后pull 然后再新建vue项目

6. 初始化项目样式

	```css
	## normalize.css
	
	/*! normalize.css v8.0.1 | MIT License | github.com/necolas/normalize.css */
	
	/* Document
	   ========================================================================== */
	
	/**
	 * 1. Correct the line height in all browsers.
	 * 2. Prevent adjustments of font size after orientation changes in iOS.
	 */
	
	html {
	  line-height: 1.15; /* 1 */
	  -webkit-text-size-adjust: 100%; /* 2 */
	}
	
	/* Sections
	   ========================================================================== */
	
	/**
	 * Remove the margin in all browsers.
	 */
	
	body {
	  margin: 0;
	}
	
	/**
	 * Render the `main` element consistently in IE.
	 */
	
	main {
	  display: block;
	}
	
	/**
	 * Correct the font size and margin on `h1` elements within `section` and
	 * `article` contexts in Chrome, Firefox, and Safari.
	 */
	
	h1 {
	  font-size: 2em;
	  margin: 0.67em 0;
	}
	
	/* Grouping content
	   ========================================================================== */
	
	/**
	 * 1. Add the correct box sizing in Firefox.
	 * 2. Show the overflow in Edge and IE.
	 */
	
	hr {
	  box-sizing: content-box; /* 1 */
	  height: 0; /* 1 */
	  overflow: visible; /* 2 */
	}
	
	/**
	 * 1. Correct the inheritance and scaling of font size in all browsers.
	 * 2. Correct the odd `em` font sizing in all browsers.
	 */
	
	pre {
	  font-family: monospace, monospace; /* 1 */
	  font-size: 1em; /* 2 */
	}
	
	/* Text-level semantics
	   ========================================================================== */
	
	/**
	 * Remove the gray background on active links in IE 10.
	 */
	
	a {
	  background-color: transparent;
	}
	
	/**
	 * 1. Remove the bottom border in Chrome 57-
	 * 2. Add the correct text decoration in Chrome, Edge, IE, Opera, and Safari.
	 */
	
	abbr[title] {
	  border-bottom: none; /* 1 */
	  text-decoration: underline; /* 2 */
	  text-decoration: underline dotted; /* 2 */
	}
	
	/**
	 * Add the correct font weight in Chrome, Edge, and Safari.
	 */
	
	b,
	strong {
	  font-weight: bolder;
	}
	
	/**
	 * 1. Correct the inheritance and scaling of font size in all browsers.
	 * 2. Correct the odd `em` font sizing in all browsers.
	 */
	
	code,
	kbd,
	samp {
	  font-family: monospace, monospace; /* 1 */
	  font-size: 1em; /* 2 */
	}
	
	/**
	 * Add the correct font size in all browsers.
	 */
	
	small {
	  font-size: 80%;
	}
	
	/**
	 * Prevent `sub` and `sup` elements from affecting the line height in
	 * all browsers.
	 */
	
	sub,
	sup {
	  font-size: 75%;
	  line-height: 0;
	  position: relative;
	  vertical-align: baseline;
	}
	
	sub {
	  bottom: -0.25em;
	}
	
	sup {
	  top: -0.5em;
	}
	
	/* Embedded content
	   ========================================================================== */
	
	/**
	 * Remove the border on images inside links in IE 10.
	 */
	
	img {
	  border-style: none;
	}
	
	/* Forms
	   ========================================================================== */
	
	/**
	 * 1. Change the font styles in all browsers.
	 * 2. Remove the margin in Firefox and Safari.
	 */
	
	button,
	input,
	optgroup,
	select,
	textarea {
	  font-family: inherit; /* 1 */
	  font-size: 100%; /* 1 */
	  line-height: 1.15; /* 1 */
	  margin: 0; /* 2 */
	}
	
	/**
	 * Show the overflow in IE.
	 * 1. Show the overflow in Edge.
	 */
	
	button,
	input { /* 1 */
	  overflow: visible;
	}
	
	/**
	 * Remove the inheritance of text transform in Edge, Firefox, and IE.
	 * 1. Remove the inheritance of text transform in Firefox.
	 */
	
	button,
	select { /* 1 */
	  text-transform: none;
	}
	
	/**
	 * Correct the inability to style clickable types in iOS and Safari.
	 */
	
	button,
	[type="button"],
	[type="reset"],
	[type="submit"] {
	  -webkit-appearance: button;
	}
	
	/**
	 * Remove the inner border and padding in Firefox.
	 */
	
	button::-moz-focus-inner,
	[type="button"]::-moz-focus-inner,
	[type="reset"]::-moz-focus-inner,
	[type="submit"]::-moz-focus-inner {
	  border-style: none;
	  padding: 0;
	}
	
	/**
	 * Restore the focus styles unset by the previous rule.
	 */
	
	button:-moz-focusring,
	[type="button"]:-moz-focusring,
	[type="reset"]:-moz-focusring,
	[type="submit"]:-moz-focusring {
	  outline: 1px dotted ButtonText;
	}
	
	/**
	 * Correct the padding in Firefox.
	 */
	
	fieldset {
	  padding: 0.35em 0.75em 0.625em;
	}
	
	/**
	 * 1. Correct the text wrapping in Edge and IE.
	 * 2. Correct the color inheritance from `fieldset` elements in IE.
	 * 3. Remove the padding so developers are not caught out when they zero out
	 *    `fieldset` elements in all browsers.
	 */
	
	legend {
	  box-sizing: border-box; /* 1 */
	  color: inherit; /* 2 */
	  display: table; /* 1 */
	  max-width: 100%; /* 1 */
	  padding: 0; /* 3 */
	  white-space: normal; /* 1 */
	}
	
	/**
	 * Add the correct vertical alignment in Chrome, Firefox, and Opera.
	 */
	
	progress {
	  vertical-align: baseline;
	}
	
	/**
	 * Remove the default vertical scrollbar in IE 10+.
	 */
	
	textarea {
	  overflow: auto;
	}
	
	/**
	 * 1. Add the correct box sizing in IE 10.
	 * 2. Remove the padding in IE 10.
	 */
	
	[type="checkbox"],
	[type="radio"] {
	  box-sizing: border-box; /* 1 */
	  padding: 0; /* 2 */
	}
	
	/**
	 * Correct the cursor style of increment and decrement buttons in Chrome.
	 */
	
	[type="number"]::-webkit-inner-spin-button,
	[type="number"]::-webkit-outer-spin-button {
	  height: auto;
	}
	
	/**
	 * 1. Correct the odd appearance in Chrome and Safari.
	 * 2. Correct the outline style in Safari.
	 */
	
	[type="search"] {
	  -webkit-appearance: textfield; /* 1 */
	  outline-offset: -2px; /* 2 */
	}
	
	/**
	 * Remove the inner padding in Chrome and Safari on macOS.
	 */
	
	[type="search"]::-webkit-search-decoration {
	  -webkit-appearance: none;
	}
	
	/**
	 * 1. Correct the inability to style clickable types in iOS and Safari.
	 * 2. Change font properties to `inherit` in Safari.
	 */
	
	::-webkit-file-upload-button {
	  -webkit-appearance: button; /* 1 */
	  font: inherit; /* 2 */
	}
	
	/* Interactive
	   ========================================================================== */
	
	/*
	 * Add the correct display in Edge, IE 10+, and Firefox.
	 */
	
	details {
	  display: block;
	}
	
	/*
	 * Add the correct display in all browsers.
	 */
	
	summary {
	  display: list-item;
	}
	
	/* Misc
	   ========================================================================== */
	
	/**
	 * Add the correct display in IE 10+.
	 */
	
	template {
	  display: none;
	}
	
	/**
	 * Add the correct display in IE 10.
	 */
	
	[hidden] {
	  display: none;
	}
	
	```

	



## 方式二

### 全局安装cli-init

`Vue脚手架 快速搭建Vue项目 `

```bash
npm install -g @vue/cli-init
```



### 初始化Vue项目

1. 新建项目文件 ==gulimall-vue==

2. 进入项目文件 

	```bash
	vue init webpack gulimall-vue
	```

	![image-20200917223419764](vue脚手架.assets/image-20200917223419764.png)

	![image-20200917223457585](vue脚手架.assets/image-20200917223457585.png)

3. 进入项目 

	```bash
	npm run dev
	```

	或者使用vue 提供的可视化界面管理项目

	```bash
	vue ui
	```

	* 打开可视化界面

		![image-20200917223737693](vue脚手架.assets/image-20200917223737693.png)

	* 首页，添加新项目

		![image-20200917223844208](vue脚手架.assets/image-20200917223844208.png)

	* 找到新建项目

		![image-20200917224057167](vue脚手架.assets/image-20200917224057167.png)

	* 启动项目

		![image-20200917224217156](vue脚手架.assets/image-20200917224217156.png)

### 整合 Element-ui

* 当前项目安装 Element-ui 

	```bash
	npm install element-ui
	```

* 引用以及使用

	```vue
	# main.js
	import 'element-ui/lib/theme-chalk/index.css'
	import ElementUI from 'element-ui'
	
	Vue.use(ElementUI)
	```

	

* 初始化项目样式：参考上面。