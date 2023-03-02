- [go map](#go-map)
	- [map初始化](#map初始化)
	- [增加数据](#增加数据)
	- [遍历数据](#遍历数据)
	- [判断kv是否存在](#判断kv是否存在)

# go map

`map`是一种无序的键值对的集合，可以通过key来快速检索数据。`map`是无序的，我们无法决定它的返回顺序，这是因为`map`的底层是使用哈希表来实现的。

`map`中的key类型必须是comparable的，也就是可以通过`==`和`!=`运算符相互比较。

- 支持`==`运算符的类型：`bool`，数值类型，`string`，指针，`channel`，以及包含它们的接口，数组和结构体。
- 不支持`==`运算符的类型：`slice`，`map`，`func`类型都不能相互比较，只能和`nil`比较。

## map初始化

在go中，字典map是引用类型，默认值为`nil`，需要使用`make`函数来创建一个实例。

```go
// 字面值初始化
m := map[string]string{
    "china":  "beijing",
    "russia": "moscow",
}

// 用make初始化，size可以忽略，那么map会申请少量的内存作为初始空间
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
