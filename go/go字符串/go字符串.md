# go字符串

大小写转换：

```go
func main() {
	str := "Rancheng"
	fmt.Println(strings.ToUpper(str))
	fmt.Println(strings.ToLower(str))
}
```

忽略大小写比较：

```go
func main() {
	str := "RANCHENG"
	str2 := "rancheng"
	fmt.Println(strings.EqualFold(str, str2))
}
```

字符串长度，可以用`utf8.RuneCountInString`来计算中文的长度：

```go
func main() {
	str1 := "ZG"
	str2 := "中国"
	fmt.Println(len(str1))  // 2
	fmt.Println(len(str2))  // 6
	fmt.Println(utf8.RuneCountInString(str)) // 2
}
```

修改字符串，可以先将字符串的内容复制到一个可写的变量中，一般是 []byte 或 []rune 类型的变量，然后再进行修改：

```go
func main() {
	s := "hello，世界"
	bs := []byte(s)
	rs := []rune(s)
	bs[0] = 'e'
	rs[6] = '神'
	fmt.Println(string(bs), string(rs))
}
```

和数值类型互换：

```go
func main() {
	n := 100
	str := strconv.Itoa(n)
	n2, _ := strconv.Atoi(str)
	fmt.Println(n, str, n2)
	f := 1.5
	str = strconv.FormatFloat(f, 'g', 'g', 64)
	f2, _ := strconv.ParseFloat(str, 64)
	fmt.Println(f, str, f2)
}
```
