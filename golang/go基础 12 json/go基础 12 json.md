- [go json](#go-json)
  - [json序列化](#json序列化)
  - [json反序列化](#json反序列化)

# go json

go标准库中`encoding/json`提供了json序列化和反序列化的接口，分别为`json.Marshal`和`json.Unmarshal`。

go中结构体提供了`tag`这一属性，可以在结构体字段的后面增加`tag`来对json序列化/反序列化的默认行为做出修改。

json对应的`tag`格式如下：

```go
Field type `json:"name,opts..."`
```

- 不指定`name`那么默认使用字段名作为序列化/反序列化名称。
- `json:"-"`表示忽略该字段，不进行序列化和反序列化。
- 有多个opt选项时，用逗号隔开：
  - `omitempty`：对应字段为零值时，不序列化。
  - `string`：对于布尔型和数值型或字符串型序列化和反序列化类型设置为json中的`string`。

## json序列化

`json.Marshal`可用于对结构体的序列化，只会序列化导出的字段，也就是大写开头的字段。

- 对于`bool`类型，序列化为json中的布尔类型，也是`true`和`false`。
- 整型，浮点型，序列化为json中的数值类型。
- `string`类型序列化为json中的`string`类型。
- 数组和切片，序列化为json中的数组，但是`[]byte`例外，序列化为base64编码的字符串。`nil`切片序列化为`null`。
- 对于指针和接口，序列化的是其指向的对象。
- 通道，复数，函数类型无法序列化和反序列化。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name   string `json:"name"`
	Gender bool   `json:"gender"`
	Age    int    `json:"age"`
	Height int    `json:"-"`
	Weight *int   `json:"weight,omitempty"`
	ID     *int
}

func main() {
	p := Person{
		Name:   "rc",
		Gender: true,
		Age:    18,
		Height: 180,
		Weight: new(int),
		ID:     nil,
	}
	bs, _ := json.Marshal(p)
	fmt.Println(string(bs)) // {"name":"rc","gender":true,"age":18,"weight":0,"ID":null}
}

```

## json反序列化

- `omitempty`选项对于反序列化没有效果。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name,omitempty"`
}

func main() {
	p := Person{
		Name: "rc",
	}

	bs := []byte(`{"name":""}`)
	json.Unmarshal(bs, &p)
	fmt.Println(p) // {}
}
```

- 对于指针类型，反序列化会自动为其在堆上创建对象，然后让指针指向它。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Age *int `json:"age"`
}

func main() {
	var p Person
	bs := []byte(`{"age":18}`)
	json.Unmarshal(bs, &p)
	fmt.Println(p, *p.Age) // {0xc000016130} 18
}

```
