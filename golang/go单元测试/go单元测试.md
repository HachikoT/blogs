- [go单元测试](#go单元测试)
  - [testing.T的方法](#testingt的方法)
  - [go test命令](#go-test命令)
- [性能测试](#性能测试)
- [参考资料](#参考资料)

# go单元测试

go单元测试代码的写法有以下几点要求：

- 文件格式: 以`_test.go`为后缀，源文件在执行`go build`时不会被构建成包的一部分，测试文件和被测试的代码文件放在一个包下。
- 函数格式：每个测试的函数都是以`Test`为函数名的前缀。
- 函数都必须传入`testing.T`类型测试对象。

示例：

```go
// sum_test.go
package main

import "testing"

func TestSum(t *testing.T) {
	if Sum(1, 1) != 2 {
		t.Error("Sum(1, 1) != 2")
	}
}

```

然后在当前目录下执行`go test -v .`命令即可进行单元测试：

```bash
PS C:\Users\11726\Documents\workspace\hello> go test -v .
=== RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
ok      hello   (cached)
```

## testing.T的方法

|                 方法                 |                   说明                   |
| :----------------------------------: | :--------------------------------------: |
|          `Log(args ...any)`          | 输出测试日志，默认不输出成功的用例的信息 |
|  `Logf(format string, args ...any)`  |                   同上                   |
|               `Fail()`               |     标记该测试用例出错了，不中断测试     |
|           `Failed() bool`            |     查询该测试用例是否已经出出过错了     |
|             `FailNow()`              |    标记该测试用例出错了，并且中断测试    |
|         `Error(args ...any)`         |             等于`Log`+`Fail`             |
| `Errorf(format string, args ...any)` |             等于`Log`+`Fail`             |
|         `Fatal(args ...any)`         |           等于`Log`+`FailNow`            |
| `Fatalf(format string, args ...any)` |           等于`Log`+`FailNow`            |
|             `SkipNow()`              |           跳过当前这个测试用例           |
|         `Skip(args ...any)`          |           等于`Log`+`SkipNow`            |
| `Skipf(format string, args ...any)`  |           等于`Logf`+`SkipNow`           |

## go test命令

- `go test -v`：显示测试过程中的详细日志，这样测试成功的测试用例也可以输出日志了。
- `go test -run="Sum"`：可以指定要执行的单元测试名字，这里采用正则匹配。
- `go test ./...`：表示从指定包递归测试下去。

# 性能测试

性能/基准测试和单元测试一样，都写在以`_test.go`结尾的文件中，不同的是：

- 每个性能测试的函数都是以`Benchmark`为函数名的前缀。
- 函数都必须传入`testing.B`类型测试对象。
- 要进行性能测试代码要放在`1-b.N`的循环中。

示例：

```go
func BenchmarkSort(b *testing.B) {
	for i := 0; i < b.N; i++ {
		nums := []int{5, 4, 3, 2, 1}
		sort.Ints(nums)
	}
}
```

然后在当前目录下执行`go test -bench=.`命令即可进行性能测试：

```bash
PS C:\Users\11726\Documents\workspace\hello> go test -bench=Sort
goos: windows
goarch: amd64
pkg: hello
cpu: Intel(R) Xeon(R) CPU E5-2666 v3 @ 2.90GHz
BenchmarkSort-20         3874023               278.9 ns/op
```

一开始，go并不知道函数要执行多久，所以先设定一个很小的N，然后增大N，最后等到测试结果稳定后，输出平均值，就得到了性能测试的结果。

还可以加上`-benchmem`选项，同时显示内存的性能：

```bash
PS C:\Users\11726\Documents\workspace\hello> go test -bench=Sort -benchmem
goos: windows
goarch: amd64
pkg: hello
cpu: Intel(R) Xeon(R) CPU E5-2666 v3 @ 2.90GHz
BenchmarkSort-20         4712967               309.6 ns/op            72 B/op          2 allocs/op
```

# 参考资料
