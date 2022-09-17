- [linux ls 命令](#linux-ls-命令)
  - [语法](#语法)
  - [常用参数](#常用参数)
  - [使用场景](#使用场景)
    - [展示隐藏文件](#展示隐藏文件)
    - [将最新修改的文件展示在前面](#将最新修改的文件展示在前面)
- [参考资料](#参考资料)

# linux ls 命令

`ls`是list的缩写，是linux中最基本的命令，用于展示目录包含的文件列表。

## 语法

```bash
ls [OPTION]... [FILE]...
```

## 常用参数

- `-a, --all`：展示所有文件，包括“`.`”开头的隐藏文件。
- `-A, --almost-all`：同`-a`，只是不展示“`.`”当前目录和“`..`”父目录。
- `-l, `：除文件名称外，也将文件类型，权限，所有者，大小，修改时间等详细信息展示出来。
- `-h, --human-readable`：和`-l`搭配使用，在展示文件大小的时候换算为可读的模式（K，M，G）。
- `-t`：以文件内容的修改时间排序，最新修改的文件排在前面，`ls`命令默认以文件名的字母顺序排序。
- `-S`：以文件大小排序，大的文件排在前面。
- `-r, --reverse`：将排序结果逆序输出。

## 使用场景

### 展示隐藏文件

```bash
[rc@localhost ~]$ ll -a
总用量 20
drwx------. 3 rc   rc   108 9月  17 20:15 .
drwxr-xr-x. 3 root root  16 9月  17 11:39 ..
-rw-------. 1 rc   rc   218 9月  17 13:42 .bash_history
-rw-r--r--. 1 rc   rc    18 8月   8 2019 .bash_logout
-rw-r--r--. 1 rc   rc   193 8月   8 2019 .bash_profile
-rw-r--r--. 1 rc   rc   231 8月   8 2019 .bashrc
```

### 将最新修改的文件展示在前面

```bash
[rc@localhost /]$ ll -t
总用量 16
drwxr-xr-x.  25 root root  720 9月  17 13:48 run
drwxr-xr-x.  75 root root 8192 9月  17 13:48 etc
drwxrwxrwt.   9 root root  245 9月  17 13:11 tmp
drwxr-xr-x.  20 root root 3240 9月  17 11:48 dev
drwxr-xr-x.  19 root root  267 9月  17 11:48 var
dr-xr-xr-x.  13 root root    0 9月  17 11:48 sys
dr-xr-xr-x. 139 root root    0 9月  17 11:48 proc
dr-xr-x---.   2 root root  114 9月  17 11:39 root
dr-xr-xr-x.   5 root root 4096 9月  17 11:39 boot
drwxr-xr-x.   3 root root   16 9月  17 11:39 home
drwxr-xr-x.  13 root root  155 9月  17 11:36 usr
lrwxrwxrwx.   1 root root    8 9月  17 11:36 sbin -> usr/sbin
lrwxrwxrwx.   1 root root    7 9月  17 11:36 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 9月  17 11:36 lib64 -> usr/lib64
lrwxrwxrwx.   1 root root    7 9月  17 11:36 bin -> usr/bin
drwxr-xr-x.   2 root root    6 4月  11 2018 media
drwxr-xr-x.   2 root root    6 4月  11 2018 mnt
drwxr-xr-x.   2 root root    6 4月  11 2018 opt
drwxr-xr-x.   2 root root    6 4月  11 2018 srv
```

# 参考资料

- [Linux ls 命令](https://www.runoob.com/linux/linux-comm-ls.html)
