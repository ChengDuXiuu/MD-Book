

## 遍历 --> Stream

>   使用流式来操作集合中的数据 ：简化代码操作度。

```java
/*
 * @Author No1.shuai
 * @Description //TODO Stream 替代  循环
 * @Date 11:30 11:30
 **/
public class Stream {
    // todo 需求 获取姓张的并且名字为三个字的元素
    private List<String> list = new ArrayList<>();
    {
        this.list.add("张三丰");
        this.list.add("张无忌");
        this.list.add("赵铭");
        this.list.add("周五寄");
        this.list.add("哈雷波涛");
        this.list.add("奇异博士");
    }

    /* 1. 循环处理 --处理次数繁杂*/
    @Test
    public void forTest(){
        List<String> temp = new ArrayList<>();
        List<String> result = new ArrayList<>();
        //处理姓张的数据
        for(String s : list){
            if (s.startsWith("张")) temp.add(s);
        }
        for (String s : temp){
            if (s.length() == 3) result.add(s);
        }
        for (String s : result){
            System.out.println(s);
        }
    }

    /* 2. Stream流式处理  */
    @Test
    public void streamTest(){
        list.stream()
                .filter(ele -> ele.startsWith("张"))
                .filter(ele -> ele.length() == 3)
                .forEach(ele -> System.out.println(ele));
    }


}
```



## Stream

> 只需要三步即可实现数据(集合、数组等)流式处理。
> 第一步：将数据源(集合、数组等)转换为Stream。   第二步：流水线式操作流。      第三步：形成新的数据。

> “集合讲的是数据，流讲的是计算！ ”

注意：

①Stream 自己不会存储元素。

②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

④Stream 属于管道流 只能操作一次，操作之后的数据就会流向下一个流，本身并不会保存数据。


#### 第一步 ：创建Stream

方式一：Collection接口被扩展，提供了两个方法

```java
default Stream<E> stream() : 返回一个顺序流

default Stream<E> parallelStream() : 返回一个并行流
```

方式二：数组创建流

```java
Arrays 的静态方法 stream()

static <T> Stream<T> stream(T[] array): 返回一个流
```

方式三：由值创建流

```java
使用静态方法 Stream.of(), 通过显示值创建一个流。它可以接收任意数量的参数。

public static<T> Stream<T> of(T... values) : 返回一个流。
```

方式四：由函数创建流：创建无限流

```java
可以使用静态方法 Stream.iterate() 和Stream.generate(), 创建无限流    

 迭代
public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)

 生成
public static<T> Stream<T> generate(Supplier<T> s)
```





## Stream常用方法

```java
/*
 * @Author No1.shuai
 * @Description //TODO Stream常用方法
 * @Date 11:58 11:58
 **/
public class StreamMethod {
    private List<String> list = new ArrayList<>();
    {
        this.list.add("张三丰");
        this.list.add("张无忌");
        this.list.add("赵铭");
        this.list.add("周五寄");
        this.list.add("哈雷波涛");
        this.list.add("奇异博士");
    }
}
```

### 1、遍历 forEach

```java
/* 遍历 */
@Test
public void foreach(){
    // 1. 生成流
    Stream<String> stream = list.stream();
    // 2. 数据处理
    stream.forEach(ele -> System.out.println(ele));
}
```



### 2、过滤 filter

```java
/* 过滤 */
@Test
public void filter(){
    // 1. 生成流
    Stream<String> stream = list.stream();
    // 2. 数据处理 -- 过滤  Predicate函数式接口
    Stream<String> stream1 = stream.filter(ele -> ele.startsWith("张"));
    // 3. 数据处理 -- 遍历
    stream1.forEach(ele -> System.out.println(ele));
}
```



### 3、映射 map

```java
/* 映射 */
@Test
public void map(){
    // 1. 生成流
    Stream<String> stream = Stream.of("a", "b", "c", "d", "e");
    // 2. 数据处理 -- 映射  Function函数式接口   R apply(T t);
    Stream<Integer> stream1 = stream.map(ele -> ele.hashCode());
    // 3. 数据处理 -- 遍历
    stream1.forEach(ele -> System.out.println(ele));
}
```



### 统计个数 count

>   是一个终结方法，即返回值并不是一个新的stream。不能继续调用stream中的其他方法

```java
 /* 统计个数 */
@Test
public void count(){
    // 1. 生成流
    Stream<String> stream = Stream.of("a", "b", "c", "d", "e");
    System.out.println(stream.count());
}
```



### 取用前几个 limit 

```java
 /* 取用前几个 */
@Test
public void limit(){
    // 1. 生成流
    Stream<String> stream = list.stream();
    // 2. 数据处理 -- 截取前几个
    Stream<String> limit = stream.limit(3);
    // 3. 数据处理 -- 遍历
    limit.forEach(ele -> System.out.println(ele));
}
```



### 跳过前几个 skip

```java
  /* 跳过前几个 */
@Test
public void skip(){
    // 1. 生成流
    Stream<String> stream = list.stream();
    // 2. 数据处理 -- 截取前几个
    Stream<String> limit = stream.skip(3);
    // 3. 数据处理 -- 遍历
    limit.forEach(ele -> System.out.println(ele));
}
```



### 组合流 concat

```java
 /* 组合流 */
@Test
public void concat(){
    // 1. 生成流 -A
    Stream<String> streamA = list.stream();
    // 2. 生成流 -B
    Stream<String> streamB = Stream.of("a", "b", "c", "d", "e");

    // 2. 数据处理 -- 组合流
    Stream<String> stream = Stream.concat(streamA,streamB);
    // 3. 数据处理 -- 遍历
    stream.forEach(ele -> System.out.println(ele));
}
```

