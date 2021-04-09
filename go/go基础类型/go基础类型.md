# 基础类型

| 类型 | 说明 |
| :---: | :---: |
| int8,int16,int32,int64<br>uint8,uint16,uint32,uint64 | 定长有符号整型 |
| int<br>uint | 一般来说，int在32位系统中是4字节，在64位系统中是8字节 |
| rune | int32的同义词，用于更清晰地表示一个unicode码 |
| byte | uint8的同义词，可以用于表示一个字符 |
| uintptr | 足够容纳指针的值的整型，用于底层编程 |
| float32 | 32位浮点型 |
| float64 | 64位浮点型 |

# 算术类型的极值

在`math`包中，定义了一些类型的极值：

```go
// Floating-point limit values.
// Max is the largest finite value representable by the type.
// SmallestNonzero is the smallest positive, non-zero value representable by the type.
const (
	MaxFloat32             = 3.40282346638528859811704183484516925440e+38  // 2**127 * (2**24 - 1) / 2**23
	SmallestNonzeroFloat32 = 1.401298464324817070923729583289916131280e-45 // 1 / 2**(127 - 1 + 23)

	MaxFloat64             = 1.797693134862315708145274237317043567981e+308 // 2**1023 * (2**53 - 1) / 2**52
	SmallestNonzeroFloat64 = 4.940656458412465441765687928682213723651e-324 // 1 / 2**(1023 - 1 + 52)
)

// Integer limit values.
const (
	MaxInt8   = 1<<7 - 1
	MinInt8   = -1 << 7
	MaxInt16  = 1<<15 - 1
	MinInt16  = -1 << 15
	MaxInt32  = 1<<31 - 1
	MinInt32  = -1 << 31
	MaxInt64  = 1<<63 - 1
	MinInt64  = -1 << 63
	MaxUint8  = 1<<8 - 1
	MaxUint16 = 1<<16 - 1
	MaxUint32 = 1<<32 - 1
	MaxUint64 = 1<<64 - 1
)
```

但是没有定义`int`类型的极值，可以自己定义：

```go
const (
    MaxInt = int(^uint(0) >> 1)
    MinInt = ^MaxInt
)
```

# 数字和字符串相互转换

可以使用`strconv`包中的`ParseXXX`和`FormatXXX`函数：

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	n, _ := strconv.ParseInt("123", 10, 0)
	str := strconv.FormatInt(n, 10)
	fmt.Println(n, str)
}

```

也可以使用`fmt.Sprintf`函数：

```go
func main() {
	var n int
	fmt.Sscanf("123", "%d", &n)
	str := fmt.Sprintf("%d", n)
	fmt.Println(n, str)
}

```

# 位运算

| 运算符 | 说明 |
| :---: | :---: |
| `^` | 当`^`作为二元运算符时表示异或操作，当作为一元运算符时表示取反操作。 |
| `&^` | 对于`z=x &^ y`表示，若y中的位为1，那么x中的位被清0，若y中的位为0，那么x中的位不变，相当于`z=x & (^y)` |
| `>>` | 对于有符号型是算术右移（填充符号位） |

# string类型

在go中，字符串类型`string`是不可修改的字节序列，默认表示utf-8编码的字符串。
在golang内部源码中，`string`的数据定义如下，所以按值传递即可，相当于C++中的`const char*`：

```go
type stringStruct struct {
	str unsafe.Pointer
	len int
}
```

需要注意的是，`string`对象可以为空（长度为0），但不会是`nil`；

# 短变量声明

短变量声明中需要注意，在新的局部块中会优先创建新的变量，看下例：

```go
func main() {
	test("rc")
}

func test(name string) {
	{
		name, ok := "rancheng", true
		// 输出rancheng
		fmt.Println(name, ok)
	}
	// 输出rc
	fmt.Println(name)
}

```

# 参考资料

- [Go string 实现原理剖析](https://blog.csdn.net/weixin_33769207/article/details/91699678)
