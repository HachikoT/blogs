# go数组

go中数组是一个由固定长度的相同类型元素组成的序列，和C++中的`std::array<T>`类似，定义语法如下：

```go
var 数组变量名 [元素数量]Type
```

需要注意的是go的数组是值类型，不能和`nil`做比较，并且拷贝的时候就是直接复制整个数组，下面是初始化规则：

```go
// 默认初始化，得到n个默认值的数组
var arr [10]int
// 字面值初始化
var arr = [3]int{1, 2, 3}
// 也可以自动推导数组长度
var arr = [...]int{1, 2, 3}
// arr[3]和arr[4]默认为零值
var arr = [5]int{1, 2, 3}
// 只有arr[4]是1，其余默认为零值
var arr = [5]int{4: 1}
```

遍历数组，可以用`range`语法，返回下标和当前元素：

```go
package main

import (
	"fmt"
)

func main() {
	arr := [...]int{1, 2, 3}
	for i, n := range arr {
		fmt.Println(i, n)
	}
}

```

当下标不存在，则会`panic`：

```go
package main

import (
	"fmt"
)

func main() {
	arr := [...]int{1, 2, 3}
	idx := 3
	fmt.Println(arr[idx])
}

```

数组的比较，若数组元素是可比较的，那么该数组也是可以比较的，`==`操作符会自动判断数组中的元素是否一一相等：

```go
package main

import (
	"fmt"
)

func main() {
	arr := [...]int{1, 2, 3}
	arr2 := [...]int{1, 2, 3}
	fmt.Println(arr == arr2)
}

```
