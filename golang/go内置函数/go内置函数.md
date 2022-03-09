- [len](#len)
- [cap](#cap)
- [make](#make)
- [append](#append)
- [copy](#copy)

# len

```go
// The len built-in function returns the length of v, according to its type:
//	Array: the number of elements in v.
//	Pointer to array: the number of elements in *v (even if v is nil).
//	Slice, or map: the number of elements in v; if v is nil, len(v) is zero.
//	String: the number of bytes in v.
//	Channel: the number of elements queued (unread) in the channel buffer;
//	         if v is nil, len(v) is zero.
// For some arguments, such as a string literal or a simple array expression, the
// result can be a constant. See the Go language specification's "Length and
// capacity" section for details.
func len(v Type) int
```

- 获取数组的长度，也可以通过数组的指针来获取长度，哪怕指针是`nil`也可以获取，因为长度信息其实是写死在类型里面的。

```go
package main

import "fmt"

func main() {
	var arr [10]int
	ptr := &arr
	var nilPtr *[10]int
    // 输出 10 10 10
	fmt.Println(len(arr), len(ptr), len(nilPtr))
}

```

- 获取切片或`map`的元素个数，如果是`nil`那么值为0。

```go
package main

import "fmt"

func main() {
	bs := []byte{1, 2, 3}
	m := map[string]string{
		"1": "1",
		"2": "2",
	}
    // 输出 3 2
	fmt.Println(len(bs), len(m))
}
```

- 获取`string`字符串中字节数。

```go
package main

import "fmt"

func main() {
    // 输出 5
	fmt.Println(len("hello"))
}

```

- 获取通道中缓存的元素个数，如果是`nil`那么值为0。

```go
package main

import "fmt"

func main() {
	ch1 := make(chan int, 10)
	var ch2 chan int
	ch1 <- 10
	// 输出 1 0
	fmt.Println(len(ch1), len(ch2))
}

```

# cap

```go
// The cap built-in function returns the capacity of v, according to its type:
//	Array: the number of elements in v (same as len(v)).
//	Pointer to array: the number of elements in *v (same as len(v)).
//	Slice: the maximum length the slice can reach when resliced;
//	if v is nil, cap(v) is zero.
//	Channel: the channel buffer capacity, in units of elements;
//	if v is nil, cap(v) is zero.
// For some arguments, such as a simple array expression, the result can be a
// constant. See the Go language specification's "Length and capacity" section for
// details.
func cap(v Type) int
```

- 获取数组的容量，数组的容量其实就等于长度。

```go
package main

import "fmt"

func main() {
	var arr [10]int
	ptr := &arr
	var nilPtr *[10]int
	// 输出 10 10 10
	fmt.Println(cap(arr), cap(ptr), cap(nilPtr))
}

```

- 获取切片所使用的底层数组的容量，`map`没有容量这个概念，所以无法使用该函数。

```go
package main

import "fmt"

func main() {
	bs := []byte{1, 2, 3}

	// 输出 3
	fmt.Println(cap(bs))
}

```

- 获取通道的容量。

```go
package main

import "fmt"

func main() {
	ch1 := make(chan int, 10)
	var ch2 chan int
	// 输出 10 0
	fmt.Println(cap(ch1), cap(ch2))
}

```

# make

```go
// The make built-in function allocates and initializes an object of type
// slice, map, or chan (only). Like new, the first argument is a type, not a
// value. Unlike new, make's return type is the same as the type of its
// argument, not a pointer to it. The specification of the result depends on
// the type:
//	Slice: The size specifies the length. The capacity of the slice is
//	equal to its length. A second integer argument may be provided to
//	specify a different capacity; it must be no smaller than the
//	length. For example, make([]int, 0, 10) allocates an underlying array
//	of size 10 and returns a slice of length 0 and capacity 10 that is
//	backed by this underlying array.
//	Map: An empty map is allocated with enough space to hold the
//	specified number of elements. The size may be omitted, in which case
//	a small starting size is allocated.
//	Channel: The channel's buffer is initialized with the specified
//	buffer capacity. If zero, or the size is omitted, the channel is
//	unbuffered.
func make(t Type, size ...IntegerType) Type
```

- 创建切片，有两种调用方式：1.指定长度`make([]T, length)`，2.同时指定长度和容量`make([]T, length, capacity)`。

```go
package main

import "fmt"

func main() {
	bs1 := make([]byte, 10)
	bs2 := make([]byte, 10, 20)
	fmt.Println(len(bs1), cap(bs1)) // 输出 10 10
	fmt.Println(len(bs2), cap(bs2)) // 输出 10 20
}

```

- 创建`map`，可以为`map`预先留足够的空间去装载指定个数的元素。

```go
package main

import "fmt"

func main() {
	m := make(map[string]string, 10)
	// 输出 0
	fmt.Println(len(m))
}

```

- 创建通道，可以指定缓冲区大小。

```go
package main

import "fmt"

func main() {
	ch := make(chan int, 10)
	// 输出 0 10
	fmt.Println(len(ch), cap(ch))
}

```

# append

```go
// The append built-in function appends elements to the end of a slice. If
// it has sufficient capacity, the destination is resliced to accommodate the
// new elements. If it does not, a new underlying array will be allocated.
// Append returns the updated slice. It is therefore necessary to store the
// result of append, often in the variable holding the slice itself:
//	slice = append(slice, elem1, elem2)
//	slice = append(slice, anotherSlice...)
// As a special case, it is legal to append a string to a byte slice, like this:
//	slice = append([]byte("hello "), "world"...)
func append(slice []Type, elems ...Type) []Type
```

- 追加数据到切片中，会创建一个新的切片，不影响以前的切片。如果底层数组容量不足那么自动进行内存分配和拷贝。

```go
package main

import "fmt"

func main() {
	bs := make([]byte, 0)
	bs2 := append(bs, []byte{1, 2, 3}...)
	fmt.Println(len(bs), cap(bs))   // 输出 0 0
	fmt.Println(len(bs2), cap(bs2)) // 输出 3 ?（容量具体多少根据底层扩容策略决定）
}

```

# copy

```go
// The copy built-in function copies elements from a source slice into a
// destination slice. (As a special case, it also will copy bytes from a
// string to a slice of bytes.) The source and destination may overlap. Copy
// returns the number of elements copied, which will be the minimum of
// len(src) and len(dst).
func copy(dst, src []Type) int
```

- 将数据从源切片拷贝到目的切片，拷贝的数量为`min(len(dst), len(src))`。

```go
package main

import "fmt"

func main() {
	bs1 := []byte{1, 2, 3, 4, 5, 6}
	bs2 := []byte{4, 5, 6}
	copy(bs1, bs2)
	fmt.Println(bs1) // 输出 4 5 6 4 5 6
	fmt.Println(bs2) // 输出 4 5 6
}

```
