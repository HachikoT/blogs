- [linux sort 命令](#linux-sort-命令)
  - [语法](#语法)
  - [常用参数](#常用参数)
  - [使用场景](#使用场景)
    - [对第2列按照数字大小排序](#对第2列按照数字大小排序)
    - [对第2列进行升序排序，对第3列进行降序排序](#对第2列进行升序排序对第3列进行降序排序)
    - [删除键值重复的行](#删除键值重复的行)
- [参考资料](#参考资料)

# linux sort 命令

`sort`命令用来对文件进行排序，支持一些简单实用的排序功能，比如按数字大小排序，按指定字段排序，去重，逆序输出等。

## 语法

```bash
sort [OPTION]... [FILE]...
```

## 常用参数

- `-f, --ignore-case`：忽略大小写进行比较。
- `-n, --numeric-sort`：按照数字大小进行排序，默认是按字母顺序。
- `-t, --field-separator=SEP`：设置分隔符。
- `-k, --key=KEYDEF`：KEYDEF的格式为`FStart[.CStart][OPTS][,FEnd[.CEnd][OPTS]]`，表示从域`FStart`的第`CStart`个字符开始到域`FEnd`的第`Cend`个字符结尾作为排序的键值（key），不写`FEnd`默认最后一个域，不写`CEnd`默认最后一个字符，可以设置多个key。这里`OPTS`选项可以设置比较的选项，表示对于该键值key怎么样排序。
- `-u, --unique`：删除键值（key）重复的行。
- `-r, --reverse`：逆序输出排序结果。

## 使用场景

### 对第2列按照数字大小排序

```bash
[rc@localhost ~]$ cat text1
zhao 300 24
wang 200 21
zhang 200 25
wu 100 22
[rc@localhost ~]$ sort -n -k 2,2 text1
wu 100 22
wang 200 21
zhang 200 25
zhao 300 24
```

### 对第2列进行升序排序，对第3列进行降序排序

```bash
[rc@localhost ~]$ cat text1
zhao 300 24
wang 200 21
zhang 200 25
wu 100 22
[rc@localhost ~]$ sort -n -k 2,2 -k 3r,3 text1
wu 100 22
zhang 200 25
wang 200 21
zhao 300 24
```

### 删除键值重复的行

```bash
[rc@localhost ~]$ cat text1
zhao 300 24
wang 200 21
zhang 200 25
wu 100 22
[rc@localhost ~]$ sort -n -u -k 2,2 text1
wu 100 22
wang 200 21
zhao 300 24
```

# 参考资料

- [Linux sort命令详解 | 深入解析 -k M.m,N.n 命令](https://blog.csdn.net/succing/article/details/120742670)
- [Linux sort命令详解：字符串排序](https://www.cnblogs.com/sunlong88/p/16356693.html)
