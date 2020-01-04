# 关于Java中equals和==
## 一，区别
[identity - What is the difference between == and equals() in Java? - Stack Overflow](https://stackoverflow.com/questions/7520432/what-is-the-difference-between-and-equals-in-java)

[Java: Integer equals vs. == - Stack Overflow](https://stackoverflow.com/questions/3637936/java-integer-equals-vs)

## 二，一个例子
下面这段代码就是有问题的，因为实际上在比较的是两个Integer Object，而他们是引用类型，==比较的是两个地址，而不是数值
```java
/** An Integer tester created by Flik Enterprises. */
public class Flik {
    public static boolean isSameNumber(Integer a, Integer b) {
        return a == b;
    }    
}
```

因此在下面这段测试代码中会报出异常。
```java
public class HorribleSteve {
    public static void main(String [] args) throws Exception {
        int i = 0;
        for (int j = 0; i < 500; ++i, ++j) {
            if (!Flik.isSameNumber(i, j)) {
                throw new Exception(
                        String.format("i:%d not same as j:%d ??", i, j));
            }
        }
        System.out.println("i is " + i);
    }
}
```



