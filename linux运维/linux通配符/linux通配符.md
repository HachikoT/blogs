- [通配符](#通配符)
- [花括号展开](#花括号展开)
- [示例](#示例)
  - [用`touch`一次创建多个文件。](#用touch一次创建多个文件)
  - [用`ls`查看多种结尾模式的文件。](#用ls查看多种结尾模式的文件)
  - [用`find`查找指定模式的文件。](#用find查找指定模式的文件)
- [参考资料](#参考资料)

# 通配符

在linux中，shell在匹配文件的时候会去解析通用模式匹配符号，shell会将匹配到的真实文件路径替换到参数输入处。需要注意的是通配符的语法和正则表达式有很大的不同。

| 通配符 | 含义 | 备注 |
| :--: | :--: | :--: |
| `*` | 匹配任意个字符 |  |
| `?` | 匹配任意单个字符 |  |
| `[set]` | 匹配`set`中的任意单一字符 | 可以使用连字符`-`表示范围，比如[0-9] |
| `[!set] or [^set]` | 匹配除了`set`中的任意单一字符 | 可以使用连字符`-`表示范围，比如[!0-9] |
| `?(PATTERN-LIST)` | 匹配字符串模式0次或1次 | 扩展通配符，需开启`extglob` |
| `*(PATTERN-LIST)` | 匹配字符串模式任意次 | 扩展通配符，需开启`extglob` |
| `+(PATTERN-LIST)` | 匹配字符串模式1次或多次 | 扩展通配符，需开启`extglob` |
| `@(PATTERN-LIST)` | 匹配字符串模式1次 | 扩展通配符，需开启`extglob` |
| `!(PATTERN-LIST)` | 匹配除`PATTERN-LIST`字符串模式 | 扩展通配符，需开启`extglob` |

# 花括号展开

也被称为大括号扩展，是可以让bash生成组合字符串的一种扩展功能。shell会将生成的所有组合结果替换到参数输入处。

| 格式 | 含义 |
| :--: | :--: |
| `{string1,string2,...,stringN}` | 可以展开为指定字符串模式 |
| `{<START>..<END>[..<INCR>]}` | 可以自动生成指定的字符串模式，可以设置起始值，终止值，和每次增量值 |

# 示例

## 用`touch`一次创建多个文件。

```bash
[rc@localhost work]$ touch text{01..10}
[rc@localhost work]$ ll
总用量 0
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text01
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text02
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text03
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text04
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text05
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text06
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text07
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text08
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text09
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text10
```

## 用`ls`查看多种结尾模式的文件。

方法一。

```bash
[rc@localhost work]$ ll text@(01|10|11)
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text01
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text10
```

方法二。

```bash
[rc@localhost work]$ ll text{01,10,11}
ls: 无法访问text11: 没有那个文件或目录
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text01
-rw-rw-r--. 1 rc rc 0 9月  20 14:15 text10
```

## 用`find`查找指定模式的文件。

```bash
[rc@localhost work]$ find . -name "text0[4-5]"
./text04
./text05
```

但是`find`不支持`extglob`，所以没法查询多种字符串模式，需要手动加`-or`参数写多个条件。

```bash
[rc@localhost work]$ touch text.{log,txt}
[rc@localhost work]$ ll
总用量 0
-rw-rw-r--. 1 rc rc 0 9月  20 14:20 text.log
-rw-rw-r--. 1 rc rc 0 9月  20 14:20 text.txt
[rc@localhost work]$ find . -name "text.@(log|txt)"
[rc@localhost work]$ find . -name "text.log" -or -name "text.txt"
./text.log
./text.txt
```

# 参考资料

- [linux通配符和正则表达式](https://blog.csdn.net/youmatterhsp/article/details/80528761)
- [bash之通配符](https://blog.csdn.net/astrotycoon/article/details/50814031)
- [bash之花括号展开（brace expansion）](https://blog.csdn.net/astrotycoon/article/details/50886676)
