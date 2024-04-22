- [pflag包](#pflag包)
	- [创建同名的flag，会报错](#创建同名的flag会报错)
	- [usage中展示的flag，默认按照字典序输出](#usage中展示的flag默认按照字典序输出)
	- [设置flag为Hidden，在usage中不展示该flag](#设置flag为hidden在usage中不展示该flag)
	- [设置flag为Deprecated，当解析到该flag的时候则显示提示信息](#设置flag为deprecated当解析到该flag的时候则显示提示信息)
	- [stringSlice类型的flag](#stringslice类型的flag)
	- [除了解析出的flag，剩下的都是arg](#除了解析出的flag剩下的都是arg)
	- [`--`后面的参数都是arg](#--后面的参数都是arg)

# pflag包

`pflag`包是专门用来处理命令行选项（flag）的工具，比go官方库`flag`功能更丰富，使用更广泛。

1. 支持POSIX/GNU风格的选项，`-f`或`--flag`。
2. 支持`deprecated`、`hidden`等高级功能。
3. 支持更多参数类型，如`ip`、`ip mask`，以及所有类型的`slice`类型。

`pflag`使用步骤如下。

```go
package main

import (
	"fmt"
	"os"
	"path"

	"github.com/spf13/pflag"
)

type options struct {
	help bool
}

var (
	flags = pflag.NewFlagSet(path.Base(os.Args[0]), pflag.ExitOnError)
	opts  = options{}
)

func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
}

func main() {
	// 1.创建绑定flag
	addFlags()

	// 2.设置usage
	flags.Usage = func() {
		fmt.Printf("Usage: %s [flags]\n", path.Base(os.Args[0]))
		flags.PrintDefaults()
	}

	// 3.解析flag
	flags.Parse(os.Args[1:])

	// 4.使用flag
	if opts.help {
		flags.Usage()
		return
	}
}

```

## 创建同名的flag，会报错

创建长名称或者短名称重复的`flag`会报错。

```go
type options struct {
	help  bool
	help2 bool
}

var (
	flags = pflag.NewFlagSet(path.Base(os.Args[0]), pflag.ExitOnError)
	opts  = options{}
)

func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.help2, "help", "h", false, "show usage 2")
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -h
main flag redefined: help
panic: main flag redefined: help

goroutine 1 [running]:
github.com/spf13/pflag.(*FlagSet).AddFlag(0xc000102100, 0xc000100280)
        /home/rc/go/pkg/mod/github.com/spf13/pflag@v1.0.5/flag.go:848 +0x5fc
github.com/spf13/pflag.(*FlagSet).VarPF(0xc000102100, {0x4e4f80, 0x5c8221}, {0x4bf1cb, 0x4}, {0x4bf108, 0x1}, {0x4c0132, 0xc})
        /home/rc/go/pkg/mod/github.com/spf13/pflag@v1.0.5/flag.go:831 +0x105
github.com/spf13/pflag.(*FlagSet).BoolVarP(0x40c310?, 0x60?, {0x4bf1cb?, 0xc0000061c0?}, {0x4bf108?, 0x406a2b?}, 0x0?, {0x4c0132?, 0x0?})
        /home/rc/go/pkg/mod/github.com/spf13/pflag@v1.0.5/bool.go:55 +0x3e
main.addFlags()
        /home/rc/workspace/pflag/main.go:23 +0x8a
main.main()
        /home/rc/workspace/pflag/main.go:28 +0x17
exit status 2
```

## usage中展示的flag，默认按照字典序输出

可以通过`flags.SortFlags`调整输出顺序模式，默认为`true`表示按照字典序输出，设置为`false`则按照代码中添加的顺序输出。

```go
type options struct {
	help    bool
	verbose bool
}

var (
	flags = pflag.NewFlagSet(path.Base(os.Args[0]), pflag.ExitOnError)
	opts  = options{}
)

func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.verbose, "verbose", "v", false, "show verbose messages")
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -h
Usage: main [flags]
  -h, --help      show usage
  -v, --verbose   show verbose messages
```

## 设置flag为Hidden，在usage中不展示该flag

```go
func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.verbose, "verbose", "v", false, "show verbose messages")

	flags.MarkHidden("help")
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -h
Usage: main [flags]
  -v, --verbose   show verbose messages
```

## 设置flag为Deprecated，当解析到该flag的时候则显示提示信息

设置`flag`为`Deprecated`，当传入的命令行中包含该`flag`的时候则显示提示信息。

```go
func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.verbose, "verbose", "v", false, "show verbose messages")
	flags.BoolVar(&opts.verbose, "debug", false, "show --debug messages")

	flags.MarkDeprecated("verbose", "use debug instead")
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -v
Flag --verbose has been deprecated, use --debug instead
```

有个问题是，当设置`flag`为`Deprecated`时，也会自动将其设置为`Hidden`，如果想在usage中仍然展示该`flag`，则需要手动设置一下。

```go
func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.verbose, "verbose", "v", false, "show verbose messages")
	flags.BoolVar(&opts.verbose, "debug", false, "show debug messages")

	flags.MarkDeprecated("verbose", "use --debug instead")
	flags.Lookup("verbose").Hidden = false
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -h
Usage: main [flags]
      --debug     show debug messages
  -h, --help      show usage
  -v, --verbose   show verbose messages (DEPRECATED: use --debug instead)
```

## stringSlice类型的flag

`stringSlice`类型的`flag`，支持多次设置`flag`，最终会合并为一个`slice`。

```go
type options struct {
	help   bool
	params []string
}

var (
	flags = pflag.NewFlagSet(path.Base(os.Args[0]), pflag.ExitOnError)
	opts  = options{}
)

func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.StringSliceVar(&opts.params, "set-param", nil, "set params")
}

func main() {
	// 1.创建绑定flag
	addFlags()

	// 2.设置usage
	flags.Usage = func() {
		fmt.Printf("Usage: %s [flags]\n", path.Base(os.Args[0]))
		flags.PrintDefaults()
	}

	// 3.解析flag
	flags.Parse(os.Args[1:])

	// 4.使用flag
	if opts.help {
		flags.Usage()
		return
	}

	fmt.Println(opts.params)
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go --set-param ver=1.0 --set-param mode=debug
[ver=1.0 mode=debug]
```

## 除了解析出的flag，剩下的都是arg

这里`flag`就是表示命令中的选项，而`arg`就是命令中的参数。

比如`rm -rf /usr`命令，`-rf`是`flag`，`/usr`是`arg`。

```go
func addFlags() {
	flags.BoolVarP(&opts.help, "help", "h", false, "show usage")
	flags.BoolVarP(&opts.verbose, "verbose", "v", false, "show verbose messages")
}

func main() {
	// 1.创建绑定flag
	addFlags()

	// 2.设置usage
	flags.Usage = func() {
		fmt.Printf("Usage: %s [flags]\n", path.Base(os.Args[0]))
		flags.PrintDefaults()
	}

	// 3.解析flag
	flags.Parse(os.Args[1:])

	// 4.使用flag
	if opts.help {
		flags.Usage()
		return
	}

	fmt.Println(flags.Args())
}
```

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -v arg1 -v arg2
[arg1 arg2]
```

## `--`后面的参数都是arg

`--`用来表示`flag`的解析结束，后面的参数都归类为`arg`。

运行结果示例：

```bash
[rc@centos pflag]$ go run main.go -- -v arg1 -v arg2
[-v arg1 -v arg2]
```
