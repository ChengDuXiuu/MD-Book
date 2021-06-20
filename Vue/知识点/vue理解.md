## 特性

* 渐进式框架

	既可以将vue当做第三方库嵌入到项目中，也可以使用vue项目

* 虚拟dom 

	相比js的dom树操作，如果js频繁操作dom树，则会引起页面重新渲染导致性能大大降低

* 双向绑定 MVVM

	MVC  ：model  controller   view

	MVP  ： view  model 之间不发生关系，有 P 来进行调度，类似于MVC中中央调度器

	MVVM  ： model  view   viewmodel    view视图    model数据   而viewmodel类似于vue 只要view变化对应的viewmodl也会发生变化导致model发生变化，反之依然

* 组件化

	文件组件、全局组件、局部组件