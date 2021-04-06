# go字典map

Map是一种无序的键值对的集合，可以通过key来快速检索数据，Map是无序的，我们无法决定它的返回顺序，这是因为Map是使用hash表来实现的。

在go中，字典map是引用类型，默认值为`nil`，下面是初始化规则：

```go
// 用make初始化，size可以忽略，那么map会申请少量的内存作为初始空间
m := make(map[string]string, size)
// 字面值初始化
m := map[string]string{
    "rc":  "cq",
    "sjj": "cq",
}
```

访问map中的元素，和c++不同，如果只是访问不存在的元素并不会创建一个kv对：

```go
// 可以用ok来判断元素是否存在
func main() {
	m := make(map[string]string)
	if _, ok := m["rc"]; ok {
		fmt.Println("exist")
	} else {
		fmt.Println("not exist")
	}
}
```

```go
// 不存在的值不会panic，而是直接返回空值
func main() {
	m := make(map[string]string)
	value = m["rc"]
}
```
