- [go字符串](#go字符串)
	- [和`[]byte`相互转换](#和byte相互转换)
	- [包含中文的时候字符计数](#包含中文的时候字符计数)
	- [遍历字符串](#遍历字符串)
	- [截取字符串](#截取字符串)
	- [字符串拼接优化](#字符串拼接优化)
	- [不区分大小写操作](#不区分大小写操作)
	- [分割字符串](#分割字符串)
- [参考资料](#参考资料)

# go字符串

go中字符串是一个不可改变的utf-8字符序列，类型为`string`。
有两点值得注意：

- `string`可以为空（长度为0），但不会是`nil`。
- string对象不可以修改。

`string`类型内部就记录了实际字符串地址和大小的数据，所以作为参数传递也是比较高效的。

## 和`[]byte`相互转换

有时为了修改字符串，或者进行数据传递解析的时候，需要`string`类型和`[]byte`类型相互进行转换。
需要注意的是，转换相当于拷贝了一遍数据，数据的修改相互不影响。

```go
package main

import "fmt"

func main() {
	str := "book"
	bs := []byte(str)
	bs[0] = 'l'
	fmt.Println(str)        // book
	fmt.Println(string(bs)) // look
}

```

## 包含中文的时候字符计数

由于`string`类型采用utf-8字符编码，所以对于中文往往是一个字符占多个字节，如果直接用`len`函数只能计算字节的数量。
想要计算中文字符数可以转换为`[]rune`类型，一个`rune`变量是四个字节大小，用来存储unicode码点。

```go
package main

import (
	"fmt"
	"unicode/utf8"
)

func main() {
	str := "hello 中国"
	fmt.Println(len(str))         // 12
	fmt.Println(len([]rune(str))) // 8
	// 用这个方法性能高一点
	fmt.Println(utf8.RuneCountInString(str)) // 8
}

```

## 遍历字符串

可以像遍历数组和切片那样遍历整个字符串，并且遍历的时候获取到的是每个字符的unicode码，也就是`rune`类型的变量。可以挨个得到中文字符的值。

```go
package main

import "fmt"

func main() {
	str := "hello 中国"
	for i, c := range str {
		fmt.Println(i, string(c))
	}
}

```

输出如下。

```bash
0 h
1 e
2 l
3 l
4 o
5  
6 中
9 国
```

## 截取字符串

和切片和数组一样，字符串也可以通过下标截取，截取之后还是`string`类型。`str[low:high]`表示截取[low,high)范围的数据，low可以超过最大下标范围，但是high超过最大下标范围会抛出`panic`。


```go
package main

import "fmt"

func main() {
	str := "hello world"
	sub := str[0:5]
	fmt.Println(sub) // hello
}

```

## 字符串拼接优化

大部分时候直接`+`起来就行，可能有的情况需要拼接的字符串太多，直接`+`起来生成太多临时对象和拷贝，可以用`string.Builder`对象来拼接，提升效率。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	var builder strings.Builder
	builder.WriteString("hello")
	builder.WriteString(" world")
	fmt.Println(builder.String())
}

```

## 不区分大小写操作

可以用`ToUpper`和`ToLower`进行大小写转换，用`EqualFold`进行忽略大小写的比较。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	fmt.Println(strings.ToUpper("Hello"))            // HELLO
	fmt.Println(strings.ToLower("Hello"))            // hello
	fmt.Println(strings.EqualFold("hello", "Hello")) // true
}

```

## 分割字符串

可以用`Split`函数指定分隔符来分割，但是`Split`不能压缩连续的分隔符，并且也不能指定多种分隔符。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	strs := strings.Split("hello world", " ")
	fmt.Printf("%#v\n", strs) // []string{"hello", "world"}
	strs = strings.Split("hello  world", " ")
	fmt.Printf("%#v\n", strs) // []string{"hello", "", "world"}
}

```

可以用`FeildsFunc`函数，这样可以压缩连续的分割符，也支持多种分隔符，但是分隔符只能是字符，而不能是字符串了。

并且语义方面也有点不同，`FeildsFunc`会忽略长度为0的字段。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	strs := strings.Split("hello  world  ", " ")
	fmt.Printf("%#v\n", strs) // []string{"hello", "", "world", "", ""}
	strs = strings.FieldsFunc("hello  world  ", func(c rune) bool { return c == ' ' })
	fmt.Printf("%#v\n", strs) // []string{"hello", "world"}
}

```

# 参考资料

- [Go 语言中关于 Unicode、Rune、UTF-8 和 string 的一个问题](https://blog.twofei.com/806/)
