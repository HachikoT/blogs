- [标准库log包](#标准库log包)
  - [写日志函数](#写日志函数)
  - [日志格式](#日志格式)
  - [日志前缀](#日志前缀)
  - [日志输出到文件](#日志输出到文件)
- [参考资料](#参考资料)

# 标准库log包

go语言标准库自带了日志log包，实现简单的日志功能。

## 写日志函数

log包中有3个系列的日志打印函数，分别`Print`系列、`Panic`系列、`Fatal`系列。

| 函数系列 |          说明          |                           作用                            |
| :------: | :--------------------: | :-------------------------------------------------------: |
| `Print`  | `Print/Printf/Println` |                       单纯打印日志                        |
| `Panic`  | `Panic/Panicf/Panicln` |                 打印日志，抛出`panic`异常                 |
| `Fatal`  | `Fatal/Fatalf/Fatalln` | 打印日志，强制结束程序(`os.Exit(1)`)，`defer`函数不会执行 |

## 日志格式

|           函数            |          说明          |
| :-----------------------: | :--------------------: |
| `func SetFlags(flag int)` | 设置日志格式标志`flag` |
|    `func Flags() int`     | 获取日志格式标志`flag` |

这里`flag`支持下面的值，多种格式用`flag1 | flag2`加起来就行：

```go

// 日志格式指定每条日志都自带的信息，后面就是具体的日志内容了，如 2009/01/23 01:23:23.123123 /a/b/c/d.go:23: message
const (
	Ldate         = 1 << iota     // the date in the local time zone: 2009/01/23
	Ltime                         // the time in the local time zone: 01:23:23
	Lmicroseconds                 // microsecond resolution: 01:23:23.123123.  assumes Ltime.
	Llongfile                     // full file name and line number: /a/b/c/d.go:23
	Lshortfile                    // final file name element and line number: d.go:23. overrides Llongfile
	LUTC                          // if Ldate or Ltime is set, use UTC rather than the local time zone
	Lmsgprefix                    // move the "prefix" from the beginning of the line to before the message
	LstdFlags     = Ldate | Ltime // initial values for the standard logger
)
```

## 日志前缀

|              函数               |        说明        |
| :-----------------------------: | :----------------: |
|     `func Prefix() string`      | 返回日志的前缀配置 |
| `func SetPrefix(prefix string)` |    设置日志前缀    |

前缀出现在一行的开头，如下面的示例：

```go
package main

import (
	"log"
)

func main() {
	log.SetPrefix("[MyLog]")
	log.Println("hello log")
}

```

输出的日志如下：

```bash
[MyLog]2023/04/08 22:48:26 hello log
```

## 日志输出到文件

可以通过`SetOutput`来指定输出的对象，一般设置为日志文件。

```go
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {
	file, err := os.OpenFile("text.log", os.O_APPEND|os.O_CREATE, 0644)
	if err != nil {
		fmt.Println("open file failed: ", err)
		return
	}
	log.SetOutput(file)
	log.Println("hello log")
}

```

# 参考资料

- [Go语言学习笔记—golang标准库log包](https://blog.csdn.net/qq_39280718/article/details/125748857)
