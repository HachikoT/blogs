- [linux grep 命令](#linux-grep-命令)
  - [语法](#语法)
  - [常用参数](#常用参数)
  - [使用场景](#使用场景)
    - [扩展语法](#扩展语法)
    - [查询当前目录下所有文件](#查询当前目录下所有文件)
- [参考资料](#参考资料)

# linux grep 命令

`grep`是一种强大的文本搜索工具，通过设置特定模式，支持正则表达式，来对文本进行匹配和输出。

## 语法

```bash
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]
```

## 常用参数

- `-E, --extended-regexp`：用扩展语法解释正则表达式。
- `-i, --ignore-case`：忽略大小写匹配。
- `-n, --line-number`：显示匹配结果所在的行号。
- `-o, --only-matching`：只输出匹配到的内容，一行内有多条匹配结果也会分多行展示。
- `-c, --count`：只输出满足匹配模式的行数，`-o`选项不会影响结果。
- `-v, --invert-match`：匹配不满足条件的行。
- `-r, --recursive`：递归查询指定目录下面的所有文件。
- `-A NUM, --after-context=NUM`：显示匹配结果及其后面NUM行数据。
- `-B NUM, --before-context=NUM`：显示匹配结果及其前面NUM行数据。
- `-C NUM, -NUM, --context=NUM`；显示匹配结果前后NUM行数据。

## 使用场景

### 扩展语法

在基本语法中，`?`，`+`，`{`，`|`，`(`，`)`失去了其正则表达式语义，而是代表这个字符本身，要使其变为正则语义，需要加反斜杠`\`来转义。

```bash
[rc@localhost work]$ cat text1 | grep ".+"
[rc@localhost work]$ cat text1 | grep ".\+"
hello world hello
123
```

但是开启扩展语法之后就不用转义符了。这样我感觉好看一些。

```bash
[rc@localhost work]$ cat text1 | grep -E ".+"
hello world hello
123
```

### 查询当前目录下所有文件

有时候需要在一堆文件里面去查找数据，这时候就可以用`-r`参数。

```bash
[rc@localhost work]$ ll
总用量 8
-rw-rw-r--. 1 rc rc  8 9月  23 13:57 text1
-rw-rw-r--. 1 rc rc 10 9月  23 13:57 text2
[rc@localhost work]$ grep -r "hello" .
./text1:hellooo
./text2:hello
```

# 参考资料

- [grep命令详解](https://www.zsythink.net/archives/1733)
