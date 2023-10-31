- [go map](#go-map)
	- [map初始化](#map初始化)
	- [增加数据](#增加数据)
	- [map的元素无法取址](#map的元素无法取址)
	- [遍历数据](#遍历数据)
	- [判断kv是否存在](#判断kv是否存在)

# go map

`map`是一种键值对的集合，可以通过key来快速检索数据。`map`是无序的，我们无法决定它的返回顺序，这是因为`map`的底层是使用哈希表来实现的。

`map`中的key类型必须是可比较的，也就是可以通过`==`和`!=`运算符比较。

|                                  可比较类型                                   |     不可比较类型      |
| :---------------------------------------------------------------------------: | :-------------------: |
| `bool`，数值类型，`string`，指针，`channel`，以及包含它们的接口，数组和结构体 | 切片，`map`，函数类型 |

## map初始化

在go中，字典map是引用类型，默认值为`nil`，需要使用`make`函数来创建一个实例。

```go
// 字面值初始化
m := map[string]string{
    "china":  "beijing",
    "russia": "moscow",
}

// 用make初始化，size可以忽略，如果忽略map会申请少量的内存作为初始空间
m := make(map[string]string, size)
```

## 增加数据

直接给指定的key赋值即可。

```go
m := make(map[string]string)
m["china"] = "beijing"
m["russia"] = "moscow"

fmt.Println(m["china"]) // 输出 beijing
```

## map的元素无法取址

直接取地址会编译报错。

```go
m := make(map[string]string)
m["china"] = "beijing"

// invalid operation: cannot take address of m["china"]
fmt.Println(&m["china"])
```

所以要修改一个已经存在的map元素，只能重新给它赋值

```go
m := make(map[string]string)
m["china"] = "beijing"

fmt.Println(m["china"]) // beijing

m["china"] = "shanghai"

fmt.Println(m["china"]) // shanghai
```

如果map的元素是结构体，可以直接通过元素直接获取字段的值，但是没办法设置字段的值（会编译报错）。

```go
type person struct{
	name string
}

m := make(map[string]person)
m["rc"] = person{"rc"}

fmt.Println(m["rc"].name) // rc

// cannot assign to struct field m["rc"].name in map
m["rc"].name = "rc2"
```

## 遍历数据

```go
m := make(map[string]string)
m["china"] = "beijing"
m["russia"] = "moscow"

for k, v := range m {
	fmt.Println(k, v)
}
```

## 判断kv是否存在

当指定的key不存在时，会返回value类型的空值。同时也可以获取第二个返回值，不存在会返回`false`。

```go
// 可以用ok来判断元素是否存在
m := make(map[string]string)
if _, ok := m["china"]; ok {
	fmt.Println("exist")
} else {
	fmt.Println("not exist")
}
```
