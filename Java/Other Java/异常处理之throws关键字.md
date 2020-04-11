# 异常处理之throws关键字

关于throws这个关键字。

```java
throws
A Java(TM) programming language keyword used in method declarations that specify which exceptions are not handled within the method but rather passed to the next higher level of the program.
```

这是来自orcale官方的教程给的说明，就是说某个method如果throws了什么异常，表明在该method内部不处理该异常。

而是交给上一层来处理。