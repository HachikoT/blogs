# 默认解释器

`#!`用于告诉操作系统该脚本要使用的shell解释器程序：

```sh
#!/bin/bash
```

常用的shell解释器有`sh`和`bash`，大部分机器上`/bin/sh`其实是`bash`，`bash`（bourne again shell）的功能比原版的`sh`强很多，一般的教程都是按照`bash`的语法来教的。

# 定义变量

- 变量赋值`=`两边不能有空格。
- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的。
- 双引号里可以有变量，双引号里可以出现转义字符。

# 处理命令行参数

- **getopts**

`getopts`命令是`shell`的内置命令，用来从参数列表检索选项以及选项参数。`getopts`配合`case`来进行操作时有两个隐含变量：一个是OPTARG，用来取当前选项参数的值，另外一个是OPTIND，代表当前选项在参数列表中的位移。OPTIND是一个特殊的变量，它的初始值是1，每次`getopts`处理完一个命令参数后就递增它，得到`getopts`要处理的下一个参数。
`getopts`不支持长选项，如果选项字符串中的字符后面带有“:”，则预期此选项将带有参数。

```sh
while getopts "a:bc" option; do
    case $option in
    a)
        echo "a: $OPTARG ($OPTIND)"
        ;;
    b)
        echo "bb ($OPTIND)"
        ;;
    c)
        echo "ccc ($OPTIND)"
        ;;
    esac
done
```
