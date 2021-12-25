- [pflag简介](#pflag简介)
- [pflag实现rm命令示例](#pflag实现rm命令示例)
- [参考资料](#参考资料)

# pflag简介

`github.com/spf13/pflag`包实现了对命令行选项进行解析的功能，虽然go语言的标准库中已经有`flag`包了，但是其功能还是比较简陋的，并且由于go语言的创始人都是plan9系统的玩家，所以命令行选项的风格和gnu的不太一样。而`pflag`就完全实现了符合gnc/posix风格的命令行选项解析效果。所以实际上大多数人都是使用`pflag`包的。
这里gnu/posix风格的命令行选项主要是同时支持长格式选项和短格式选项，并且短格式选项支持连写，比如我们平时使用的`rm -rf xxx`。

# pflag实现rm命令示例

```go
package main

import (
	"fmt"
	"os"

	"github.com/spf13/pflag"
)

func main() {
	var (
		force     bool
		recursive bool
		needHelp  bool
		fileNames []string
	)

	// 设置命令行选项
	pflag.BoolVarP(&force, "force", "f", false, "强制删除")
	pflag.BoolVarP(&recursive, "recursive", "r", false, "递归删除目录及其内容")
	pflag.BoolVarP(&needHelp, "help", "h", false, "显示此帮助信息并退出")

	// 设置完之后就可以进行解析了
	pflag.Parse()

	// 输出用法说明
	if needHelp {
		pflag.Usage = func() {
			fmt.Fprintln(os.Stderr, "用法：go-rm [选项]... [文件]...")
			pflag.PrintDefaults()
		}
		pflag.Usage()
		return
	}

	// 获取除了命令行选项剩下的命令行参数
	if pflag.NArg() == 0 {
		fmt.Fprintln(os.Stderr, "没有指定文件名")
		os.Exit(2)
	}
	fileNames = pflag.Args()

	fmt.Println(force, recursive, needHelp, fileNames)
}

```

# 参考资料

- [flag与pflag标准库的使用 ](https://www.dazhuanlan.com/liuyangsl/topics/1517311)
- [用go实现linux命令行](https://blog.csdn.net/caijhBlog/article/details/78265811)
