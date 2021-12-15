- [go数组](#go数组)
	- [初始化](#初始化)
	- [访问不存在的元素会panic](#访问不存在的元素会panic)
	- [for range遍历数组](#for-range遍历数组)
	- [数组的比较](#数组的比较)
- [参考资料](#参考资料)

# go数组

数组是一组相同类型元素的线性序列，在go中，数组是值类型，所以拷贝时效率较低。并且数组的大小也是类型的一部分，所以`[5]T`和`[10]T`是不同的数组类型。

## 初始化

| 方式 | 代码示例 |
| :-: | :-: |
| 默认值初始化，所有元素为默认值 | `var arr [10]int` |
| 字面值初始化 | `var arr = [3]int{1, 2, 3}` |
| 也可以自动推导数组长度 | `var arr = [...]int{1, 2, 3}` |
| 指定特定元素的值，其余元素还是默认值 | `var arr = [3]int{2: 1}` |

## 访问不存在的元素会panic

```go
func main() {
	var ns [10]int
	for i := 0; i <= len(ns); i++ {
		// 当i等于10时，抛出panic: runtime error: index out of range [10] with length 10
		fmt.Println(ns[i])
	}
}
```

## for range遍历数组

```go
func main() {
	var ns [10]int
	for idx, n := range ns {
		fmt.Println(idx, n)
	}
}
```

## 数组的比较

若两个数组的类型是一样的，并且数组元素是可比较的，那么该数组也是可以比较的，`==`操作符会自动判断数组中的元素是否一一相等。

```go
func main() {
	var ns [10]int
	var ns2 [10]int
	fmt.Println(ns == ns2)
}
```

# 参考资料

- [Golang 入门 : 数组](https://blog.csdn.net/dupeng0811/article/details/89876287)
