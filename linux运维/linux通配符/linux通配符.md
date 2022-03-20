- [通配符](#通配符)
- [示例](#示例)
- [参考资料](#参考资料)

# 通配符

在linux中，shell在匹配文件的时候会去解析通用模式匹配符号。语法和正则表达式有区别。

| 通配符 | 含义 |
| :--: | :--: |
| `*` | 匹配任意个字符 |
| `?` | 匹配任意单个字符 |
| `[list]` | 匹配`list`中的任意单一字符 |
| `[!list] or [^list]` | 匹配除了`list`中的任意单一字符 |
| `[c1-c2]` | 匹配`c1-c2`中的任意单一字符 如：`[0-9] [a-z]` |
| `{string1,string2,...}` | 匹配`sring1`或`string2`(或更多)其一个字符串 |

# 示例

用`touch`一次创建多个文件。

```bash
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  20 15:16 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
rc@rc-virtual-machine:~/tmp$ touch test{1,2,3,4,5}
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  20 16:01 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
-rw-rw-r--  1 rc rc    0 3月  20 16:01 test1
-rw-rw-r--  1 rc rc    0 3月  20 16:01 test2
-rw-rw-r--  1 rc rc    0 3月  20 16:01 test3
-rw-rw-r--  1 rc rc    0 3月  20 16:01 test4
-rw-rw-r--  1 rc rc    0 3月  20 16:01 test5
```

用`ls`查看指定模式的文件。

```bash
rc@rc-virtual-machine:~/tmp$ ll test[1-3]
-rw-rw-r-- 1 rc rc 0 3月  20 16:01 test1
-rw-rw-r-- 1 rc rc 0 3月  20 16:01 test2
-rw-rw-r-- 1 rc rc 0 3月  20 16:01 test3
```

用`find`查找指定模式的文件。

```bash
rc@rc-virtual-machine:~/tmp$ find . -name "test[4-5]"
./test5
./test4
```

测试的时候发现`find`好像不支持`{string1,string2,...}`这种模式，需要手动加`-o`参数写入多个条件。

```bash
rc@rc-virtual-machine:~/tmp$ touch test.{log,txt}
rc@rc-virtual-machine:~/tmp$ ll
总用量 8
drwxrwxr-x  2 rc rc 4096 3月  20 16:05 ./
drwxr-xr-x 31 rc rc 4096 3月  20 15:11 ../
-rw-rw-r--  1 rc rc    0 3月  20 16:05 test.log
-rw-rw-r--  1 rc rc    0 3月  20 16:05 test.txt
rc@rc-virtual-machine:~/tmp$ find . -name "*.{log,txt}"
rc@rc-virtual-machine:~/tmp$ find . -name "*.log" -o -name "*.txt"
./test.txt
./test.log

```

# 参考资料

- [linux通配符和正则表达式](https://blog.csdn.net/youmatterhsp/article/details/80528761)
