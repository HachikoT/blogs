- [打开文件](#打开文件)
	- [os.OpenFile](#osopenfile)
	- [os.Open](#osopen)
	- [os.Create](#oscreate)
- [判断文件是否存在](#判断文件是否存在)
- [读取文件](#读取文件)
	- [一次性读取小文件全部内容](#一次性读取小文件全部内容)
		- [os.ReadFile](#osreadfile)
		- [ioutil.ReadAll](#ioutilreadall)
	- [按行读取文件](#按行读取文件)
		- [bufio.Reader的ReadBytes方法](#bufioreader的readbytes方法)
		- [bufio.Reader的ReadString方法](#bufioreader的readstring方法)
		- [bufio.Scanner](#bufioscanner)
	- [按字节数读取文件](#按字节数读取文件)
- [拼接目录](#拼接目录)

# 打开文件

## os.OpenFile

`OpenFile`是通用的打开文件函数，支持设定打开模式和文件权限。

```go
func OpenFile(name string, flag int, perm FileMode) (*File, error)
```

支持的打开模式。

|  打开模式  |                说明                |
| :--------: | :--------------------------------: |
| `O_RDONLY` |            只读模式打开            |
| `O_WRONLY` |            只写模式打开            |
|  `O_RDWR`  |            读写模式打开            |
| `O_APPEND` |              追加模式              |
| `O_CREATE` |         当文件不存在则创建         |
|  `O_EXCL`  | 搭配`O_CREATE`使用，确保文件不存在 |
|  `O_SYNC`  |             同步io模式             |
| `O_TRUNC`  |   文件存在则截断（清空）文件内容   |

支持的文件权限。

```go
const (
	// The single letters are the abbreviations
	// used by the String method's formatting.
	ModeDir        = fs.ModeDir        // d: is a directory
	ModeAppend     = fs.ModeAppend     // a: append-only
	ModeExclusive  = fs.ModeExclusive  // l: exclusive use
	ModeTemporary  = fs.ModeTemporary  // T: temporary file; Plan 9 only
	ModeSymlink    = fs.ModeSymlink    // L: symbolic link
	ModeDevice     = fs.ModeDevice     // D: device file
	ModeNamedPipe  = fs.ModeNamedPipe  // p: named pipe (FIFO)
	ModeSocket     = fs.ModeSocket     // S: Unix domain socket
	ModeSetuid     = fs.ModeSetuid     // u: setuid
	ModeSetgid     = fs.ModeSetgid     // g: setgid
	ModeCharDevice = fs.ModeCharDevice // c: Unix character device, when ModeDevice is set
	ModeSticky     = fs.ModeSticky     // t: sticky
	ModeIrregular  = fs.ModeIrregular  // ?: non-regular file; nothing else is known about this file

	// Mask for the type bits. For regular files, none will be set.
	ModeType = fs.ModeType

	ModePerm = fs.ModePerm // Unix permission bits, 0o777
)
```

## os.Open

`Open`就是简单地用只读的方式打开已存在的文件。

```go
func Open(name string) (*File, error) {
	return OpenFile(name, O_RDONLY, 0)
}
```

## os.Create

`Create`用读写的方式打开或创建新的文件。创建的文件的权限是`rw-rw-rw-`，所有人都可读写。当然还要和umask掩码计算，一般umask为`0002`，所以计算下来权限是`rw-rw-r--`，也还是有被同组的用户修改文件的风险，需要注意。

```go
func Create(name string) (*File, error) {
	return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}
```

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

# 读取文件

## 一次性读取小文件全部内容

### os.ReadFile

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	content, err := os.ReadFile("./tmp.txt")
	if err != nil {
		fmt.Printf("read file failed: %v\n", err)
		return
	}
	fmt.Println(string(content))
}

```

### ioutil.ReadAll

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	file, err := os.Open("./tmp.txt")
	if err != nil {
		fmt.Printf("open file failed: %v\n", err)
		return
	}
	defer file.Close()

	content, err := ioutil.ReadAll(file)
	if err != nil {
		fmt.Printf("read file failed: %v\n", err)
		return
	}

	fmt.Println(string(content))
}
```

## 按行读取文件

### bufio.Reader的ReadBytes方法

`ReadBytes`一直读取直到遇到`delim`指定的字节，返回已经读取到的字节（包含`delim`字节）。

`ReadBytes`不会自动将windows下的`\r\n`转换为`\n`。是按照二进制的方式读取的。

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {
	file, err := os.Open("./tmp.txt")
	if err != nil {
		fmt.Printf("open file failed: %v\n", err)
		return
	}
	defer file.Close()

	reader := bufio.NewReader(file)

	for {
		line, err := reader.ReadBytes('\n')
		if err != nil && err != io.EOF {
			fmt.Printf("read file failed: %v\n", err)
			return
		}
		fmt.Print(string(line))
		if err == io.EOF {
			break
		}
	}
}

```

### bufio.Reader的ReadString方法

和`ReadBytes`类似，也不会自动将windows下的`\r\n`转换为`\n`。返回的是`string`类型。

### bufio.Scanner

Scanner功能更强大，可以对其进行格式化解析。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("./tmp.txt")
	if err != nil {
		fmt.Printf("open file failed: %v\n", err)
		return
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)

	for scanner.Scan() {
		line := scanner.Text()
		fmt.Println(line)
	}

	if scanner.Err() != nil {
		fmt.Printf("read file failed: %v\n", scanner.Err())
		return
	}
}

```

## 按字节数读取文件

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {
	file, err := os.Open("./tmp.txt")
	if err != nil {
		fmt.Printf("open file failed: %v\n", err)
		return
	}
	defer file.Close()

	reader := bufio.NewReader(file)

	buf := make([]byte, 1024)
	for {
		n, err := reader.Read(buf)
		if err != nil && err != io.EOF {
			fmt.Printf("read file failed: %v\n", err)
			return
		}
		if n == 0 {
			break
		}
		fmt.Print(string(buf[:n]))
	}
}

```

# 拼接目录

`path.Join`可以拼接目录，并且自动忽略多余的`/`：

```go
// 输出 data/rc
fmt.Println(path.Join("data", "//rc"))
```
