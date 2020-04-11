# In 关键字

这个关键字相当于

```C#
ref readonly
```

[参考StackOverFlow上的解释。](https://stackoverflow.com/questions/52820372/why-would-one-ever-use-the-in-parameter-modifier-in-c)

`in` was recently introduced to the C# language.

`in` is actually a `ref readonly`. Generally speaking, there is only one use case where `in` can be helpful: high performance apps dealing with lots of large `readonly struct`s.

Assuming you have:

```cs
readonly struct VeryLarge
{
    public readonly long Value1;   
    public readonly long Value2;

    public long Compute() { }
    // etc
}
```

and

```cs
void Process(in VeryLarge value) { }
```

In that case, the `VeryLarge` struct will be passed by-reference without creating of defensive copies when using this struct in the `Process` method (e.g. when calling `value.Compute()`), and the struct immutability is ensured by the compiler.

Note that passing a not-readonly `struct` with an `in` modifier will cause the compiler to *create a defensive copy* when calling struct's methods and accessing properties in the `Process` method above, which will negatively affect performance!

There is a really good [MSDN blog entry](https://blogs.msdn.microsoft.com/seteplia/2018/03/07/the-in-modifier-and-the-readonly-structs-in-c/) which I recommend to carefully read.

If you would like to get some more historical background of `in`-introducing, you could read this [discussion](https://github.com/dotnet/csharplang/issues/1133) in the C# language's GitHub repository.

In general, most developers agree that introducing of `in` could be seen as a mistake. It's a rather exotic language feature and can only be useful in high-perf edge cases.