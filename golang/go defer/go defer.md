- [defer](#defer)
  - [规则一：在函数退出时执行](#规则一在函数退出时执行)
  - [规则二：当defer被声明时，其参数就会被实时解析](#规则二当defer被声明时其参数就会被实时解析)
  - [规则三：defer执行顺序为先进后出](#规则三defer执行顺序为先进后出)
- [参考资料](#参考资料)

# defer

go语言中的`defer`可以将指定的语句延迟执行，用于实现资源的回收。

## 规则一：在函数退出时执行

需要注意，`defer`后的语句是在函数退出时执行，而不是类似C++中RAII那样离开块作用域执行。
下面的代码最终输出`hello world`。

```go
package main

import "fmt"

func main() {
	if true {
		defer fmt.Println("world")
	}

	fmt.Printf("hello ")
}

```

## 规则二：当defer被声明时，其参数就会被实时解析

下面的代码最终输出`0`，因为`n`作为参数传入的时候就已经被解析了。

```go
package main

import "fmt"

func main() {
	n := 0
	defer fmt.Println(n)
	n++
}

```

## 规则三：defer执行顺序为先进后出

多条`defer`语句就会进行压栈，先声明的先执行。
下面的代码输出`3 2 1 0`。

```go
package main

import "fmt"

func main() {
	for i := 0; i < 4; i++ {
		defer fmt.Println(i)
	}
}

```

# 参考资料

- [golang中defer的使用规则](https://studygolang.com/articles/10167)
- [Go语言defer（延迟执行语句）](http://c.biancheng.net/view/61.html)
