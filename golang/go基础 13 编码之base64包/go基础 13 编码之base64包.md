- [base64编码](#base64编码)
- [go base64](#go-base64)
  - [编码](#编码)
  - [解码](#解码)

# base64编码

base64是一种字节编码方式，将3个8bit字节（24bit）编码为4个6bit字节（24位），并从ASCII码表中选择64个可打印的字符作为base64的字符集。

这样就可以通过可读的纯文本的方式传输，保存二进制数据了。

# go base64

go中支持4种base64编码。

|     编码方式     |                    说明                     |                             编码字符集                             | 填充字符 |
| :--------------: | :-----------------------------------------: | :----------------------------------------------------------------: | :------: |
|  `StdEncoding`   |               标准base64编码                | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/` |   `=`    |
|  `URLEncoding`   |          支持写到url中的base64编码          | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_` |   `=`    |
| `RawStdEncoding` |      标准base64编码，但是不带填充字符       | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/` |    无    |
| `RawURLEncoding` | 支持写到url中的base64编码，但是不带填充字符 | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_` |    无    |

## 编码

```go
func main() {
	input := []byte("hello world")
	output := base64.StdEncoding.EncodeToString(input)
	fmt.Println(output) // 输出 aGVsbG8gd29ybGQ=
}
```

## 解码

```go
func main() {
	input := "aGVsbG8gd29ybGQ="
	output, err := base64.StdEncoding.DecodeString(input)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(output)) // 输出 hello world
}
```
