### 介绍

<a>官网：https://cli.vuejs.org/zh/guide/installation.html</a>

Vue CLI是一个官方发布 vue.js 项目脚手架使用 vue-cli 可以快速搭建Vue开发环境以及对应的webpack配置.

如果你只是简单写几个Vue的Demo程序, 那么你不需要Vue CLI.
如果你在开发大型项目, 那么你需要, 并且必然需要使用Vue CLI

### 使用前提

NodeJs  - - 依赖环境
Webpack - - 对所有资源优化压缩等操作

### 初始化项目

Vue CLI2初始化项目

    vue init webpack my-project

Vue CLI3 4初始化项目

    vue create my-project

### Runtime-Compiler和Runtime-only的区别

1. 如果在之后的开发中，你依然使用template，就需要选择Runtime-Compiler
2. 如果你之后的开发中，使用的是.vue文件夹开发，那么可以选择Runtime-only
