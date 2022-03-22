- [通配符](#通配符)
- [花括号展开](#花括号展开)
- [示例](#示例)
- [参考资料](#参考资料)

# 通配符

在linux中，shell在匹配文件的时候会去解析通用模式匹配符号。语法和正则表达式有区别。

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

花括号扩展有时在别的书籍中也被称为大括号扩展，是可以让bash生成任意字符串的一种扩展功能。

| 格式 | 含义 |
| :--: | :--: |
| `{string1,string2,...,stringN}` | 可以展开为指定字符串模式 |
| `{<START>..<END>[..<INCR>]}` | 可以自动生成指定的字符串模式 |

# 示例

用`touch`一次创建多个文件。

```bash
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  22 11:54 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
rc@rc-virtual-machine:~/tmp$ touch test{01..10}
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  22 11:54 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test01
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test02
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test03
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test04
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test05
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test06
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test07
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test08
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test09
-rw-rw-r--  1 rc rc    0 3月  22 11:54 test10
```

用`ls`查看指定模式的文件。

```bash
rc@rc-virtual-machine:~/tmp$ ll test@(01|10|11)
-rw-rw-r-- 1 rc rc 0 3月  22 11:54 test01
-rw-rw-r-- 1 rc rc 0 3月  22 11:54 test10
```

用`find`查找指定模式的文件。

```bash
rc@rc-virtual-machine:~/tmp$ find . -name "test0[4-5]"
./test04
./test05
```

但是`find`不支持`extglob`，所以没法查询多种字符串模式，需要手动加`-o`参数写多个条件。

```bash
rc@rc-virtual-machine:~/tmp$ touch test.{log,txt}
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  22 11:58 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
-rw-rw-r--  1 rc rc    0 3月  22 11:58 test.log
-rw-rw-r--  1 rc rc    0 3月  22 11:58 test.txt
rc@rc-virtual-machine:~/tmp$ find . -name "test.@(log|txt)"
rc@rc-virtual-machine:~/tmp$ find . -name "test.log" -o -name "test.txt"
./test.txt
./test.log
```

# 参考资料

- [linux通配符和正则表达式](https://blog.csdn.net/youmatterhsp/article/details/80528761)
- [bash之通配符](https://blog.csdn.net/astrotycoon/article/details/50814031)
- [bash之花括号展开（brace expansion）](https://blog.csdn.net/astrotycoon/article/details/50886676)
