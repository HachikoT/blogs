# 判断文件是否存在

```go
func PathExist(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, nil
	}
	return false, err
}
```

# 按行读取文件

```go
func main() {
	f, err := os.Open("tmp.txt")
	if err != nil {
		fmt.Println("open file failed")
		return
	}
	defer f.Close()

	scanner := bufio.NewScanner(f)
	for scanner.Scan() {
		line := scanner.Text()
		fmt.Println(line)
	}
	err = scanner.Err()
	if err != nil && err != io.EOF {
		fmt.Println("read file failed")
		return
	}
}
```
