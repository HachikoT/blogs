- [linux find 命令](#linux-find-命令)
  - [语法](#语法)
  - [常用查找表达式](#常用查找表达式)
    - [按文件名查找](#按文件名查找)
    - [按文件类型查找](#按文件类型查找)
    - [按文件大小查找](#按文件大小查找)
    - [按修改日期查找](#按修改日期查找)
    - [按属猪查找](#按属猪查找)
    - [按文件权限查找](#按文件权限查找)
    - [带或的条件表达式](#带或的条件表达式)
- [参考资料](#参考资料)

# linux find 命令

`find`命令可以在指定的目录下查找符合条件的文件。可以按照文件名查找（-name），文件类型查找（-type），文件大小查找（-size），修改日期查找（-mtime），按属主查找（-user），按文件权限查找（-perm）等。

## 语法

```bash
find [-H] [-L] [-P] [-D debugopts] [-Olevel] [path...] [expression]
```

## 常用查找表达式

### 按文件名查找

支持通配符，但是使用通配符的时候必须要用引号包起来，不然会报语法错误，因为shell在展开通配符的时候会替换到输入参数，所以必须使用`find`自己代码内部的通配符功能来解析。

```bash
[rc@localhost work]$ ll
总用量 0
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -name *sh
find: 路径必须在表达式之前: lock.sh
用法: find [-H] [-L] [-P] [-Olevel] [-D help|tree|search|stat|rates|opt|exec] [path...] [expression]
[rc@localhost work]$ find . -name "*sh"
./hello.sh
./lock.sh
```

顺便一提，`-iname`可以忽略大小写按文件名查找。

```bash
[rc@localhost work]$ ll
总用量 0
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -iname "*.SH"
./hello.sh
./lock.sh
```

### 按文件类型查找

这里需要注意，普通文件类型的符号是`f`，而不是平时展示的`-`，其余的跟平时展示的一样。

```bash
[rc@localhost work]$ ll
总用量 0
drwxrwxr-x. 2 rc rc 6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc 0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -type f
./hello.sh
./lock.sh
```

### 按文件大小查找

首先需要说明，对于数字参数前面的符号，`+`表示大于，`-`表示小于，没有表示等于。
按照文件大小查找，支持的字节单位有`k`（小写），`M`，`G`。

```bash
[rc@localhost work]$ ll -h
总用量 4.0M
-rw-rw-r--. 1 rc rc 4.0M 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc    6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc    0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc    0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -size +1M
./bigfile
```

### 按修改日期查找

查找30分钟之内修改过的文件。

```bash
[rc@localhost work]$ ll
总用量 4096
-rw-rw-r--. 1 rc rc 4194304 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc       6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -mmin -30
.
./dir1
./bigfile
```

查找2天前的日志文件，这里就展示下语法，暂时没有2天前的日志文件，可以看到多个查询条件并列可以忽略`-and`，但是如果是或的形式就必须写`-or`。

```bash
[rc@localhost work]$ ll
总用量 4096
-rw-rw-r--. 1 rc rc 4194304 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc       6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -mtime +2 -name "*.log"
```

### 按属猪查找

```bash
[rc@localhost work]$ ll
总用量 4096
-rw-rw-r--. 1 rc rc 4194304 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc       6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -user rc
.
./hello.sh
./lock.sh
./dir1
./bigfile
```

### 按文件权限查找

权限前面带一个`-`号，表示对其他权限不做要求。

```bash
[rc@localhost work]$ ll
总用量 4096
-rw-rw-r--. 1 rc rc 4194304 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc       6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -perm -u=x
.
./dir1
```

### 带或的条件表达式

```bash
[rc@localhost work]$ ll
总用量 4096
-rw-rw-r--. 1 rc rc 4194304 9月  20 17:20 bigfile
drwxrwxr-x. 2 rc rc       6 9月  20 17:09 dir1
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 hello.sh
-rw-rw-r--. 1 rc rc       0 9月  20 16:49 lock.sh
[rc@localhost work]$ find . -name "*.sh" -or -type d
.
./hello.sh
./lock.sh
./dir1
```

# 参考资料

- [Linux find 命令](https://www.runoob.com/linux/linux-comm-find.html)
