- [接口 interface](#接口-interface)
  - [接口比较](#接口比较)
  - [类型断言](#类型断言)
  - [空接口 interface{}](#空接口-interface)
- [参考资料](#参考资料)

# 接口 interface

go语言使用interface来实现多态，并且是非侵入的，不需要具体的类型来继承接口，只需要具体的类型提供指定的方法就可以了。

比如下面的代码，对于正方形和圆形都实现了计算面积的`area`方法，所以可以说`square`和`circle`实现了接口`shape`。

```go
package main

import (
	"fmt"
	"math"
)

type square struct {
	side float64
}

type circle struct {
	radius float64
}

type shape interface {
	area() float64
}

func (s square) area() float64 {
	return s.side * s.side
}

func (c circle) area() float64 {
	return math.Pi * c.radius * c.radius
}

func info(z shape) {
	fmt.Println(z)
	fmt.Println(z.area())
}

func main() {
	s := square{10}
	c := circle{5}
	info(s)
	info(c)
}

```

## 接口比较

接口值是可比较的。如果两个接口值具有相同的动态类型并且动态值是可比较的并且动态值相等，或者两个接口值都为`nil`则它们相等。

```go
package main

import (
	"fmt"
)

type bird struct {
}

type flyable interface {
	fly()
}

func (s bird) fly() {
	fmt.Println("fly")
}

func main() {
	var f flyable = bird{}
	var f2 flyable = bird{}
	fmt.Println(f == f2) // true
}

```

## 类型断言

对于一个接口对象，可以使用类型断言来进行类型判断和转换。使用语法`value, ok := i.(type)`来实现。

```go
	var n int = 100
	var i interface{} = n
	// 判断是否为该类型，如果不是nn就是默认值，并且ok为false，不会panic
	nn, ok := i.(int)
	// 判断是否为该类型，如果不是那么会panic
	mm := i.(int)
```

## 空接口 interface{}

空接口没有任何要实现的方法，所以任何类型都可以用空接口保存，go语言用它来实现传入任意类型的值，比如`fmt.Println`。

```go
type any = interface{}

func Println(a ...any) (n int, err error) {
	return Fprintln(os.Stdout, a...)
}
```

咱们自己也可以这样来实现传入任意的参数的函数。

```go
package main

import "fmt"

func myPrint(args ...interface{}) {
	for i, value := range args {
		// 这时value的类型其实还是interface{}
		fmt.Printf("idx: %d type: %T value: %v\n", i, value, value)
		fmt.Println()
	}
}

func main() {
	// idx: 0 type: int value: 12
	// idx: 1 type: string value: haha
	myPrint(12, "haha")
}

```

# 参考资料

