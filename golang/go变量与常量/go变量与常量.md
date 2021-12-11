- [变量](#变量)
  - [变量声明](#变量声明)
  - [作用域与变量覆盖](#作用域与变量覆盖)
- [常量](#常量)
  - [常量声明](#常量声明)
  - [iota](#iota)
- [参考资料](#参考资料)

# 变量

## 变量声明

一般形式的变量声明，可以同时声明多个同类型变量，初始化为默认值或者可以手动赋初始值。

```go
// case1
var identifier type
// case2
var identifier1, identifier2 type
// case3
var identifier1, identifier2 int = 1, 2
```

类型自动推导，可以同时声明多个不同类型的变量。

```go
// case1
var identifier = 1
// case2
var identifier1, identifier2 = 1, "hello"
// case3
var (
	identifier1 = 1
	identifier2 = "hello"
)
```

简短类型声明，等号左边必须包含至少一个新的变量名，不然会报错。

```go
// case1
identifier := 1
// case2
identifier1, identifier2 := 1, "hello"
```

## 作用域与变量覆盖

当进入子作用域的时候，可以使用同名的变量，这时可以起到覆盖的作用。

```go
func main() {
	n := 1
	{
		n := "hello"
		fmt.Println(n) // 输出hello
	}
	fmt.Println(n) // 输出1
}
```

这个规则遇到简短类型声明多个变量的时候也是成立的，子作用域中还是新的变量。

```go
func main() {
	n, m := 1, 2
	{
		n, k := 3, 4
		fmt.Println(n, k) // 输出3, 4
	}
	fmt.Println(n, m) // 输出1, 2
}
```

# 常量

## 常量声明

常量的声明和变量是一样的，只是前面的`var`换成了`const`，并且常量必须在声明的时候赋初值。

```go
// case1
const identifier int = 1
// case2
const identifier1, identifier2 int = 1, 2
// case3
const identifier = 1
// case4
const identifier1, identifier2 = 1, "hello"
// case5
const (
	identifier1 = 1
	identifier2 = "hello"
)
```

## iota

在上面`case5`中，常量可以不指定右侧的表达式，但第一个常量名必须指定，忽略右侧表达式的常量相当于重写了一遍上一个常量右侧的表达式。

```go
const (
    thumb = 1
    index
    middle = 2
    ring
    pinky
)

fmt.Println(thumb, index, middle, ring, pinky) // 输出1 1 2 2 2
```

`iota`在go中是特殊的关键字，表示常数生成器。`iota`从0开始，每隔一行就增加1。

```go
const (
    thumb = iota
    index
    middle
    ring
    pinky
)

fmt.Println(thumb, index, middle, ring, pinky) // 输出0 1 2 3 4
```

中途使用`iota`，这里就看出`iota`其实就是代表当前所在行数。

```go
const (
    thumb = 100
    index
    middle = iota
    ring
    pinky
)

fmt.Println(thumb, index, middle, ring, pinky) // 输出100 100 2 3 4
```

# 参考资料

- [Go 语言变量](https://www.runoob.com/go/go-variables.html)
- [golang 使用 iota ](https://studygolang.com/articles/2192)
