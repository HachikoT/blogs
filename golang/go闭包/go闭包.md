- [go闭包](#go闭包)
- [go闭包遇到循环](#go闭包遇到循环)
- [参考资料](#参考资料)

# go闭包

在go语言中，函数是“一等公民”，所谓“一等公民”是指函数可以作为类型，函数对象可以像普通对象那样用来赋值，作为参数传递，作为返回值返回。
而go的闭包则是函数的高阶应用，所谓闭包是指引用了自由变量的匿名函数，被引用的自由变量和匿名函数对象一同存在，即使已经离开了自由变量的环境也不会被释放或者删除，在闭包中可以继续使用这个自由变量。

```text
匿名函数 + 自用变量引用 = 闭包
```

举例：

```go
package main

import "fmt"

func getSequence() func() int {
	i := 0
	return func() int {
		i += 1
		return i
	}
}

func main() {
	nextNumber1 := getSequence()

	fmt.Println(nextNumber1()) // 1
	fmt.Println(nextNumber1()) // 2
	fmt.Println(nextNumber1()) // 3

	// 下面又重新从1开始计数，是因为重新调用了getSequence函数，此时返回的闭包函数引用的局部变量是一个新的变量
	nextNumber2 := getSequence()
	fmt.Println(nextNumber2()) // 1
	fmt.Println(nextNumber2()) // 2
}
```

# go闭包遇到循环

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	s := []string{"a", "b", "c"}
	for _, v := range s {
		go func() {
			fmt.Println(v)
		}()
	}
	time.Sleep(time.Second * 1)
}

```

当循环中需要捕获自由变量的时候，常常会因为捕获到的是同一个变量，导致得不到预期结果（预期是每个闭包引用的是本次循环得到的值）。

- 方案一：重新定义一个局部变量

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	s := []string{"a", "b", "c"}
	for _, v := range s {
		v := v
		go func() {
			fmt.Println(v)
		}()
	}
	time.Sleep(time.Second * 1)
}

```

- 方案二：作为参数传进去，不过这样就没有自由变量了

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	s := []string{"a", "b", "c"}
	for _, v := range s {
		go func(v string) {
			fmt.Println(v)
		}(v)
	}
	time.Sleep(time.Second * 1)
}

```

# 参考资料

- [Go语言闭包](http://c.biancheng.net/view/59.html)
- [Go 语言函数闭包](https://www.runoob.com/go/go-function-closures.html)
