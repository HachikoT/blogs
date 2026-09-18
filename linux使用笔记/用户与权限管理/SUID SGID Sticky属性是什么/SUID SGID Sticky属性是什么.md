[toc]

# SUID、SGID、Sticky 属性

Linux 普通权限之外还有三种常见的特殊权限位：SUID、SGID 和 Sticky。它们分别用于改变可执行文件的身份、目录中新建对象的属组继承规则，以及共享目录中的删除和重命名权限。

特殊权限位不是额外的“万能权限”。它们只在特定的文件类型和操作上生效，实际访问结果还会受到 ACL、Linux capabilities、SELinux/AppArmor、挂载选项和进程自身逻辑的影响。

| 权限位 | 八进制值 | 典型对象         | 主要作用                                           |
| :----: | :------: | :--------------- | :------------------------------------------------- |
|  SUID  |  `4000`  | 可执行文件       | 执行时使用文件属主作为有效用户                     |
|  SGID  |  `2000`  | 可执行文件、目录 | 文件执行时使用文件属组；目录中新建对象继承目录属组 |
| Sticky |  `1000`  | 目录             | 限制用户删除或重命名其他用户的文件                 |

## SUID：以文件属主身份执行

对可执行文件设置 SUID 后，普通用户执行该程序时，进程的有效用户通常会变成文件属主，而不是启动程序的用户。典型显示形式是属主权限中的 `s`：

```bash
rc@ubuntu:~$ ll /usr/bin/passwd 
-rwsr-xr-x 1 root root 68208 Feb  6  2024 /usr/bin/passwd*
```

普通用户没有权限直接读写 `/etc/shadow`。但普通用户又必须能修改自己的密码，所以 passwd 设置 SUID。

当属主执行权限不存在但 SUID 存在时，权限位通常显示为大写 `S`：

```bash
-rwSr-xr-x root root program
```

## SGID：继承属组或以文件属组身份执行

### SGID 文件

对可执行文件设置 SGID 后，程序执行时的有效组通常会变成文件属组：

```bash
rc@ubuntu:~$ sudo chmod g+s program 
rc@ubuntu:~$ ll program 
-rwxr-sr-x 1 root root 0 Sep 18 06:36 program*
```

SGID 文件的风险模型与 SUID 类似，但影响范围是组权限。应重点检查文件属组是否可信，以及程序是否会使用该组权限读取、写入或执行文件。

### SGID 目录

对目录设置 SGID 后，在目录中创建的普通文件和子目录通常继承该目录的属组。这适合团队共享目录：

```bash
rc@ubuntu:~$ sudo groupadd app-team
rc@ubuntu:~$ sudo mkdir -p /srv/project
rc@ubuntu:~$ sudo chown root:app-team /srv/project
rc@ubuntu:~$ sudo chmod 2777 /srv/project
rc@ubuntu:~$ ll /srv/
total 12
drwxr-xr-x  3 root root     4096 Sep 18 06:48 ./
drwxr-xr-x 20 root root     4096 Sep 11 08:00 ../
drwxrwsrwx  2 root app-team 4096 Sep 18 06:48 project/
```

加入 `app-team` 的用户在 `/srv/project` 中创建文件时，文件属组通常会继承为 `app-team`，而不是创建者的主组。还应结合 `umask` 或默认 ACL 控制新文件是否对组可读写。

```bash
rc@ubuntu:/srv/project$ touch test
rc@ubuntu:/srv/project$ ll
total 8
drwxrwsrwx 2 root app-team 4096 Sep 18 06:50 ./
drwxr-xr-x 3 root root     4096 Sep 18 06:48 ../
-rw-rw-r-- 1 rc   app-team    0 Sep 18 06:50 test
```

## Sticky：目录粘滞位

Sticky 位最常用于多人可写目录，例如 `/tmp`：

```bash
rc@ubuntu:~$ ll -d /tmp
drwxrwxrwt 20 root root 4096 Sep 18 06:47 /tmp/
```

目录拥有写权限时，用户可以创建、删除和重命名目录项。设置 Sticky 位后，删除或重命名文件还需要满足以下条件之一：

- 操作者是该文件的属主。
- 操作者是该目录的属主。
- 操作者是 `root` 或拥有相应管理权限。

Sticky 位不限制文件内容读取和写入，也不会赋予用户任何提权能力。它只是限制共享目录中的删除和重命名。
