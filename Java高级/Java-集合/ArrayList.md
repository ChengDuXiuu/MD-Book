## 知识点

*   扩容缩容
*   存放元素顺序 ：有序
*   线程是否安全 ： 不安全
*   数组结构 ：数组
*   查询  ： 下标







## 为什么下标查询

`源码:`

```java
public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
}
E elementData(int index) {
    return (E) elementData[index];
}
```

`解读:`

*   其原理由数组实现，也就是使用 Object[] elementData 来存放元素

*   因此使用数组下标直接查询是最快的，O(n)=1

*   默认初始化容量是10

    ```java
    /**
         * Default initial capacity.
         */
    private static final int DEFAULT_CAPACITY = 10;
    ```



## 扩容

*   扩容

    ```java
    Arrays.copyOf(elementData, newCapacity);
    //如果newCapacity为20 之前size为默认值10，扩容本质就是拷贝，则旧数组前10个进行拷贝到新的数组，新数组后10个元素为null
    ```

    ```java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1); // 进行扩容
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    ```

*   扩容多大呢？

    ```java
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 进行扩容   右移一位  1.5倍  10->15
    ```

    

## 缩容

>   上面我们了解了 当容量大过size 则会进行扩容，扩容为右移一位  即原来的1.5倍



### 1、如何删除

`源码`

```java
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```

*   使用元素进行删除，可以理解，因为让用户提供下标不现实
*   删除需要迭代，很慢。并且删除后缩容



## 时间复杂度

*   O(1)
*   O(n)
*   O(log n)  二分、二叉树、红黑树





## ArrayList和Vector区别

`相同`

*   都是使用数组实现

`不同点`

*   ArrayList 不安全，而Vector 是线程安全的 所有的方法都使用了 synchronized
*   ArrayList 扩容 右移一位 1.5倍，而Vector 则扩容两倍  并且可以自定义



