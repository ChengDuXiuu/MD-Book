## 1、特性

*   String类使用final修饰，不可变行、不可被继承

    >   即一旦被创建，对其的任何操作【拼接，掐头去尾等】都需要重新开辟内存空间

*   实现Serialzable、Compareable接口，拥有序列化和排序特性

*   jdk8底层由char【】构成，jdk9改为了byte【】构成

    >   缘由 : char占两个字节，而绝大多数字符串是阿拉伯数字以及字母组成，因此需要一个字节byte就足够了。
    >
    >   针对中文以及甚至更多字节的情况，String类定义了 private final byte coder 用来存放当前内容的编码，从而采用是字符还是字节







## 2、创建











