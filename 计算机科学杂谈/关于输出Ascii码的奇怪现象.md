# 关于输出Ascii码的奇怪现象

在做题的时候，发现了一个奇怪的现象。

使用的是Java语言。如下所示，把一个Char数组转换成String然后输出。

注意这些char的值都是ASCII码

```java
char[] cc = {20,21,22,11,12,13,32,33,34,0,0};
System.out.print(String.valueOf(cc));
```

输出如下，三个字符，空格，叹号和冒号

```
 !"
```

这三个字符分别对应了32，33，34三个ASCII码值。

也就是说ASCII码中的控制字符是不会输出出来的，如果要强行输出。显示就是类似乱码的东西。

```java
char[] cc = {20,21,22,11,12,0,0};
System.out.print(String.valueOf(cc));
```

输出如下

```
  
```

