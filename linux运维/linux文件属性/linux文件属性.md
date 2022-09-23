- [linux文件属性](#linux文件属性)
  - [文件类型](#文件类型)
  - [文件权限](#文件权限)
    - [文件默认权限](#文件默认权限)
    - [修改文件默认权限](#修改文件默认权限)
  - [文件时间属性](#文件时间属性)
  - [有效用户和有效群组](#有效用户和有效群组)
- [参考资料](#参考资料)

# linux文件属性

当使用`ls -l`命令展示目录的时候，会显示文件类型，文件权限，属主，属组，文件内容修改时间等信息。

```bash
rc@rc-virtual-machine:/$ ls -l
drwxr-xr-x   4 root root       4096 2月  19 06:43 boot/
drwxrwxr-x   2 root root       4096 11月 28 12:17 cdrom/
drwxr-xr-x  20 root root       4260 2月  25 19:12 dev/
drwxr-xr-x 132 root root      12288 2月  25 06:50 etc/
drwxr-xr-x   3 root root       4096 11月 28 12:21 home/
drwx------   2 root root      16384 11月 28 12:15 lost+found/
drwxr-xr-x   2 root root       4096 8月  19  2021 media/
drwxr-xr-x   2 root root       4096 8月  19  2021 mnt/
drwxr-xr-x   3 root root       4096 12月  2 15:11 opt/
dr-xr-xr-x 408 root root          0 2月  25 19:12 proc/
drwx------   7 root root       4096 1月  24 16:29 root/
drwxr-xr-x  36 root root       1020 2月  27 06:37 run/
drwxr-xr-x  12 root root       4096 12月  1 20:24 snap/
drwxr-xr-x   2 root root       4096 8月  19  2021 srv/
dr-xr-xr-x  13 root root          0 2月  25 19:12 sys/
drwxrwxrwt  42 root root      16384 2月  27 12:27 tmp/
drwxr-xr-x  14 root root       4096 8月  19  2021 usr/
drwxr-xr-x  14 root root       4096 8月  19  2021 var/
```

每一行对应一个文件的属性。

| 文件类型与权限 | 链接数 | 所属用户 | 所属组 | 文件大小 | 文件内容修改时间 | 文件名 |
| :------------: | :----: | :------: | :----: | :------: | :--------------: | :----: |
|   drwxr-xr-x   |   3    |   root   |  root  |   4096   |  11月 28 12:21   | home/  |

## 文件类型

在`ls -l`展示的文件类型与文件权限中，第一个字符代表文件的类型

|    示例    | 文件类型 | 文件权限  |
| :--------: | :------: | :-------: |
| drwxr-xr-x |    d     | rwxr-xr-x |

文件类型如下。

- `d`：目录（directory）。
- `-`：普通文件。
- `l`：链接文件（link）。
- `b`：块设备文件，比如硬盘（block）。
- `c`：字符设备，比如键盘鼠标（character）。
- `s`：套接字文件（socket）。
- `p`：管道文件（pipe）。

## 文件权限

文件权限分为三组，从左到右分别是属主权限（user），属组权限（group），其他（others）。

|    示例    | 属主权限（users） | 属组权限（group） | 其他用户权限（others） |
| :--------: | :---------------: | :---------------: | :--------------------: |
| drwxr-xr-- |        rwx        |        r-x        |          r--           |

对于文件和目录来说，权限代表的意义不一样。

| 文件类型 |                r                 |                        w                         |             x              |
| :------: | :------------------------------: | :----------------------------------------------: | :------------------------: |
|   文件   |             文件可读             |                     文件可写                     |         文件可执行         |
|   目录   | 目录可读（可以查看目录下的文件） | 目录可写（可以增删目录下的文件，可以重命名目录） | 目录可执行（可以进入目录） |

### 文件默认权限

`umask`表示新建文件或者目录的时候用来屏蔽初始权限的掩码。
创建文件和目录的时候默认的权限不一样：

- 文件的初始权限：`-rw-rw-rw-`
- 目录的初始权限：`drwxrwxrwx`

初始权限再去除`umask`指定的权限就得到新建目录或者文件实际的权限了。

```bash
[rc@localhost work]$ umask
0002
[rc@localhost work]$ touch text1
[rc@localhost work]$ mkdir dir1
[rc@localhost work]$ ll
总用量 0
drwxrwxr-x. 2 rc rc 6 9月  23 10:24 dir1
-rw-rw-r--. 1 rc rc 0 9月  23 10:24 text1
```

可以看到文件和目录的`others`中的写权限`w`都被`umask`屏蔽掉了。

### 修改文件默认权限

- 临时修改：直接在命令行输入`umask xxxx`即可.
- 永久修改：可以在`/etc/profile`文件添加`umask xxxx`即可。

还可以根据不同用户类型来设置不同的`umask`值，下面就是centos7中`/etc/profile`自带的设置。

```bash
if [ $UID -gt 199 ] && [ "`/usr/bin/id -gn`" = "`/usr/bin/id -un`" ]; then
    # 普通用户，uid>=200
    umask 002
else
    # 系统用户，uid<200
    umask 022
fi
```

## 文件时间属性

用`stat`命令可以查看文件详细的时间属性。

```bash
[rc@localhost work]$ stat text1
  文件："text1"
  大小：0               块：0          IO 块：4096   普通空文件
设备：fd00h/64768d      Inode：67487003    硬链接：1
权限：(0664/-rw-rw-r--)  Uid：( 1000/      rc)   Gid：( 1000/      rc)
环境：unconfined_u:object_r:user_home_t:s0
最近访问：2022-09-23 10:24:51.687207891 +0800
最近更改：2022-09-23 10:24:51.687207891 +0800
最近改动：2022-09-23 10:24:51.687207891 +0800
创建时间：-
```

在linux中文件有三种时间属性。

- 最近访问时间（access time）（atime）：文件被读取的时候会更新，比如`cat`命令查看文件。
- 最近内容修改时间（modification time）（mtime）：文件写入的时候会更新，比如`vi`命令修改文件。
- 最近状态修改时间（change time）（ctime）：文件属性变化的时候会更新，比如`chmod`命令修改文件属性。

`touch`命令默认会更新这三种时间属性。

## 有效用户和有效群组

`passwd`命令需要查看`/etc/shadow`文件，但这个文件需要`root`权限才可以读写，那么一般用户怎么通过`passwd`命令修改密码呢。

```bash
[rc@localhost work]$ which passwd
/usr/bin/passwd
[rc@localhost work]$ ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 27856 8月   9 2019 /usr/bin/passwd
```

linux通过设置开启有效用户的概念来解决这种问题。通过`chmod 4755 xxx`命令最前面的八进制数字来开启`SUID`位，表示该可执行程序使用文件属主来作为有效有户。内核实际在检查权限的时候会根据有效用户和有效组来判断。

- `SUID`：值为4，表示是否开启有效用户功能，使用文件属主作为有效用户。
- `GUID`：值为2，表示是否开启有效群组功能，使用文件属组作为有效组。
- `SBIT`：值为1，暂时没了解。

# 参考资料

- [Linux下七种文件类型、文件属性及其查看方法](https://blog.csdn.net/rong09_13/article/details/79233956)
- [Linux | 文件的时间属性](https://zhuanlan.zhihu.com/p/108055568)
