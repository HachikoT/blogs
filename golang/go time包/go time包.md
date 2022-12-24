- [time结构体](#time结构体)
- [转换为unix时间戳](#转换为unix时间戳)
- [转换为字符串](#转换为字符串)
- [时间的加减](#时间的加减)
- [参考资料](#参考资料)

# time结构体

通过`time.Now()`可以获得当前的时间，返回结果是一个`time.Time`结构体，里面不仅包含了时间戳，也包含了用来展示该时间的时区信息。

```go
type Time struct {
	wall uint64
	ext  int64
	loc *Location
}
```

# 转换为unix时间戳

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println(time.Now().Unix())
}

```

# 转换为字符串

time包中支持字符串和日期的相互转换：

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	myLayout := "2006-01-02 15:04:05"
	fmt.Println(t.Format(myLayout))

	str := t.Format(myLayout)
	t, _ = time.ParseInLocation(myLayout, str, time.Local)
	fmt.Println(t.Format(myLayout))
}

```

这里`2006-01-02 15:04:05`在go语言中就是用来定义日期格式的字符串，和其它语言中的`yyyy-mm-dd HH:MM:ss`的作用一样，只是go语言的开发者觉得这样可读性更好，一眼就可以看出实际的展示效果是怎么样的。

time包中自己定义了一些常用的日期转换格式。

```go
const (
	Layout      = "01/02 03:04:05PM '06 -0700" // The reference time, in numerical order.
	ANSIC       = "Mon Jan _2 15:04:05 2006"
	UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
	RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
	RFC822      = "02 Jan 06 15:04 MST"
	RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
	RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
	RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
	RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
	RFC3339     = "2006-01-02T15:04:05Z07:00"
	RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
	Kitchen     = "3:04PM"
	// Handy time stamps.
	Stamp      = "Jan _2 15:04:05"
	StampMilli = "Jan _2 15:04:05.000"
	StampMicro = "Jan _2 15:04:05.000000"
	StampNano  = "Jan _2 15:04:05.000000000"
)
```

# 时间的加减

用`Duration`类型来记录一段时间，可以用来实现对时间的加减操作。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t1 := time.Now()
	t2 := t1.Add(time.Hour * 2)
	du := t2.Sub(t1)
	fmt.Println(du)
}
```

# 参考资料
