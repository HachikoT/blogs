# go常量

在go中，常量是一个编译期就可以确定的值，并且只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型，这样是为了在编译期确定值，类似于C++的`constexpr`。
常量名可以不指定右侧的表达式，但第一个常量名必须指定，忽略右侧表达式的常量名相当于重写了一遍上一个常量值的右侧表达式：

```go
const (
    a = 1
    b
    c = 2
    d
)

fmt.Println(a, b, c, d) // 1 1 2 2
```

`iota`在go中是特殊的关键字，表示常数生成器。`iota`从0开始，每隔一行就增加1：

```go
// 对应0,1,2,3,4,5,6
const (
    Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

中途使用`iota`，这里就看出`iota`其实就是代表的行数：

```go
// 对应0,1,2,3,4
const (
	zero  = 0
	one   = 1
	two   = 2
	three = iota
	four  = iota
)
```

# 参考资料

- [golang 使用 iota ](https://studygolang.com/articles/2192)
