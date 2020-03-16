## final使用

final使用场景有四种

- final变量 具体内容在下面
- 空白final (只声明在构造器中初始化)
- final参数 (和final变量保持一致)
- final方法 不能被重写且不会被子类覆盖

>private修饰的方法隐式制定final
<font color=ff00ff>final方法和static方法属于前期绑定，因此不能改变。其余方法都为后期绑定</font>
- final类：不能再继承，final类中所有的方法隐式指定final
#### final类
    不能被继承没有子类，final类中所有的方法隐式指定final

#### final方法
    方法不能被子类的方法覆盖，但可以被继承。private修饰的方法隐式制定final

#### final变量
  * 空白final：申明处未初始化，在构造方法中初始化或者运行期初始化。保证在编译器初始化否则编译不通过
  ```java
    private Random random=new Random(45);
    private final String baseStr;//构造方法中初始化
    private final StringBuilder builder;//构造方法中初始化
    final int baseInt=15;
    private final int a=random.nextInt();//运行期初始化
  //    private final int a;  编译不通过 定义出未初始化并且构造方法中也没初始化
    base(String baseStr,StringBuilder builder){
        this.baseStr=baseStr;//必须初始化在编译期
        this.builder=builder;
    }
    public void method(){
        //        baseInt+5;//报错：基本数据类型  变量及内容都不可变
        System.out.println("builder对象地址改变前："+builder.hashCode());
        builder.append("dsf");//对象 变量为地址，地址不可变但其内容可变
        System.out.println("builder对象地址改变后："+builder.hashCode());

        //        baseStr+"fds";//string 本身为对象理因可改变内容，但其本身使用final修饰不可变并且对于字符串的操作都会重新生成一个新的字符串
    }
  ```
  > 输出结果
  >> builder对象地址改变前：1209271652
  builder对象地址改变后：1209271652

- final变量:引用地址不可变对象内容可变，如上介绍了三种final情况 `基本数据类型` `String类型` `对象`

- final参数 (和final变量保持一致)

```java
  /**
  * TODO:<p>  <p/>
  *
  * @package: packages
  * @Author mac
  * @Date 2020/1/5 8:06 下午
  * @Version V1.0
  **/
  public class FinalClass {
    private final base base;
    FinalClass(base base){
        this.base=base;//必须初始化在编译期
    }
    public int  method(final StringBuilder builder,final String string,final int a){
        builder.append("sdf");
  //        string+"fds"; //报错
  //        a+5;          //报错
        return a+5;//新对象
    }

  }
```
