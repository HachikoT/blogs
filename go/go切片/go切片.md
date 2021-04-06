# go切片Slice

![go-slice](go-slice.png)

切片slice是对数组array的引用，多个slice可以指向相同的数组，并且支持动态扩展长度，这一点类似于C++中的`std::vector<T>`。
在go中，切片slice是引用类型，默认值为`nil`，下面是初始化规则：

```go
// 默认初始化（nil）
var s []int
// 字面值初始化
s := []int{}
s := []int{1, 2, 3}
// 用make函数初始化
s := make([]int, len, cap)
// 从已有数组或者切片中引用，表示截取[startIndex, endIndex)
s := array[startIndex:endIndex]
s := slice[startIndex:endIndex]
```

endIndex可以为`len(s)`：

```go
func main() {
	s := []int{1, 2, 3, 4}
	// 截取出空切片
	s2 := s[len(s):len(s)]
	// panic: runtime error
	s3 := s[len(s)+1:]
}
```

nil切片和空切片：

```go
func main() {
    s1 := []int{}
    var s2 []int
    fmt.Println(s1 == nil) //false
    fmt.Println(s2 == nil) //true
}
```

qppend追加元素：slice进行元素的追加的时候，使用系统的append函数，这时行为和C++中的`std::vector<T>`的`push_back`类似，即超出当前容量后，会以2倍的增速重新申请空间，得到一个新的底层数组：

```go
func main() {
    s1 := []int{}
    s1 = append(s1, 1, 2, 3)
    fmt.Println(s1) // [1 2 3]
}
```

如何检查两个切片是否相等？只能自己循环比较每一个元素是否相等，但是对于`[]byte`切片，则可以用`bytes.Equal`接口来判断：

```go
package main

import (
	"bytes"
	"fmt"
)

func main() {
	s := []int{1, 2, 3, 4, 5, 6}
	s2 := []int{1, 2, 3, 3, 5, 6}
	equalFalg := true
	for i, n := range s {
		if n != s2[i] {
			equalFalg = false
			break
		}
	}
	fmt.Println(equalFalg)
	b := []byte{1, 2, 3}
	b2 := []byte{1, 2, 3}
	fmt.Println(bytes.Equal(b, b2))
}

```
