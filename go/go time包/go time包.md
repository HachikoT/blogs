# go time包

time包中支持字符串和日期的相互转换：

```go
func main() {
    str := "2021-04-17 23:26:00"
    t, _ := time.Parse("2006-01-02 15:04:05", str)
    fmt.Println(t.Format("2006-01-02 15:04:05"))
}
```

这里`2006-01-02 15:04:05`在go语言中就是用来定义日期格式的字符串，和其它语言中的`yyyy-mm-dd HH:MM:ss`的作用一样，只是go语言的开发者觉得这样可读性更好，一眼就可以看出实际的展示效果是怎么样的。
在时间类型`Time`中，其实还包括一个字段`Location`，表名格式化的时候对应哪个世区：

```go
type Time struct {
	wall uint64
	ext  int64
	loc *Location
}
```

需要注意的是，`Parse`方法解析的时候如果没有指明时区，那么使用的是`UTC`时区，和咱们平时用的上海时区差了8小时。
