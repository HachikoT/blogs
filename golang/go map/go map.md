- [go map](#go-map)
	- [map初始化](#map初始化)
	- [增加数据](#增加数据)
	- [遍历数据](#遍历数据)
	- [判断kv是否存在](#判断kv是否存在)

# go map

`map`是一种无序的键值对的集合，可以通过key来快速检索数据，`map`是无序的，我们无法决定它的返回顺序，这是因为`map`是使用hash表来实现的。
`map`中的key类型必须是comparable的，所以golang中的`bool`，`int`，`string`，`interface`等都可以，但是`map`，`func`，`slice`等类型不可以通过`==`运算符比较，所以不可以作为key类型。
`interface`是可以比较的，比较时会先判断实际类型是否相同，然后比较实际类型，如果实际类型不可比较那么会抛出异常。

```go
package main

import "fmt"

func main() {
	var i interface{} = func() {}
	var i2 interface{} = func() {}
	// 抛出panic: runtime error: comparing uncomparable type func()
	fmt.Println(i == i2)
}

```

## map初始化

在go中，字典map是引用类型，默认值为`nil`，下面是初始化规则。

```go
// 用make初始化，size可以忽略，那么map会申请少量的内存作为初始空间
m := make(map[string]string, size)
// 字面值初始化
m := map[string]string{
    "china":  "beijing",
    "russia": "moscow",
}
```

## 增加数据

```go
package main

import "fmt"

func main() {
	m := make(map[string]string)
	m["china"] = "beijing"
	m["russia"] = "moscow"
	fmt.Println(m["china"])
}

```

## 遍历数据

```go
package main

import "fmt"

func main() {
	m := make(map[string]string)
	m["china"] = "beijing"
	m["russia"] = "moscow"
	for k, v := range m {
		fmt.Println(k, v)
	}
}

```

## 判断kv是否存在

当指定的key不存在时，会返回value类型的空值。同时也可以获取第二个返回值，不存在会返回`false`。

```go
// 可以用ok来判断元素是否存在
func main() {
	m := make(map[string]string)
	if _, ok := m["china"]; ok {
		fmt.Println("exist")
	} else {
		fmt.Println("not exist")
	}
}

```
