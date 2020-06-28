### 为何使用模块化

随着js代码数量不断地增多，无可避免的增加了变量名冲突的问题。虽然可以使用闭包或匿名内部类解决问题。但是模块化开发是一个趋势。

模块化规范：

> CommonJS、AMD、CMD，也有ES6的Modules

ES6导入导出

```JavaScript
modelA.js

export let flagA=true;
export function testA(a,b){
    return a+b;
};
export function demoA(a,b){
    return a*b;
};
export class PersonA {
    constructor(name,age) {
        this.name=name;
        this.age=age;
    }
    fun(){
        console.log(this.name+"》》》》"+this.age);
    }
}
```

```JavaScript
modelB.js

export let flagB=true;
export function testB(a,b){
    return a+b;
};
export function demoB(a,b){
    return a*b;
};
export class PersonB {
    constructor(name,age) {
        this.name=name;
        this.age=age;
    }
    fun(){
        console.log(this.name+"》》》》"+this.age);
    }
}
```

```HTML
<body>
    <script src="modelA.js" type="module"></script>
    <script src="modelB.js" type="module"></script>

    <script type="module">
        import * as modelA from "./modelA.js"
        import * as modelB from "./modelB.js"
        console.log(modelA.flagA+">>"+modelA.demoA(1,2)+">>"+modelA.testA(3,4)+">>");
        console.log(modelB.flagB+">>"+modelB.demoB(5,6)+">>"+modelB.testB(7,8)+">>");
        var personA=new modelA.PersonA("张三",15);
        personA.fun();
        var personB=new modelB.PersonB("张三",15);
        personB.fun();
    </script>
</body>
```

#### CommonJS 使用

```JavaScript
//CommonJS导出
model.exports={
  flag:false,
  test(a,b){
    return a+b;
  },
  demo(a,b){
    return a*b;
  }
}
//CommonJS导入
let {test,demo,flag} =require('aaa');
```
