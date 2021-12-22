- [switch语法](#switch语法)
- [参考资料](#参考资料)

# switch语法

在go中，`switch`可以支持传入各种类型进行选择，从实现上来说相当于从第1个`case`到最后一个`case`遍历进行比较，执行最先满足条件的`case`。
并且go语言还规定必须要显式写明`fallthrough`语句才会接着执行下一个`case`，不用再写`break`了。这样其实更合理一些，因为绝大部分情况都是这个语义的，何必再在每个`case`里面写上`break`呢。

```go
func main() {
	var weakDay string
	fmt.Scan(&weakDay)
	switch weakDay {
	case "Mon.":
		fmt.Println(1)
	case "Tues.":
		fmt.Println(2)
	case "Wed.":
		fmt.Println(3)
	case "Thurs.":
		fmt.Println(4)
	case "Fri.":
		fmt.Println(5)
	case "Sat.":
		fmt.Println(6)
	case "Sun.":
		fmt.Println(7)
	default:
		panic("wrong day")
	}
}
```

支持一个`case`里面多种情况。

```go
func main() {
	var weakDay string
	fmt.Scan(&weakDay)
	switch weakDay {
	case "Mon.", "Tues.", "Wed.", "Thurs.", "Fri.":
		fmt.Println("work day")
	case "Sat.", "Sun.":
		fmt.Println("weakend")
	default:
		panic("wrong day")
	}
}
```

可以不传入参数，替代繁琐的`if else`写法。这里每个`case`挨个判断，进入最先满足条件的`case`执行。

```go
func main() {
	var score int
	fmt.Scan(&score)
	switch {
	case score < 60:
		fmt.Println("不及格")
	case score >= 60 && score < 80:
		fmt.Println("不错")
	case score >= 80:
		fmt.Println("优秀")
	default:
		panic("wrong number")
	}
}
```

# 参考资料

- [Go 语言 switch 语句](https://www.runoob.com/go/go-switch-statement.html)
