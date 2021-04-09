# go类型断言（Type Assertion）

`x.(T)`检查x的动态类型是否是T，其中x必须是接口值。

```go
func main() {
	var n int = 100
	var i interface{} = n
	// 判断是否为该类型
	nn, ok := i.(int)
	// 判断是否为该类型，如果不是那么会panic
	mm := i.(int)
}
```

`type switch`的语法如下：


```go
switch x.(type){
case nil: // 如果x是nil
case int, uint: 
case bool:
case string;
default: //没有匹配上
}
//case的顺序是有意义的，因为可能同时满足多个接口，不可以用fallthrough, default的位置无所谓。
```

# 参考资料

- [go语言的类型断言](https://www.jianshu.com/p/6a46fc7b6e5b)
