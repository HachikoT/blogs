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

```text
-rwsr-xr-x root root /usr/bin/passwd
```

普通用户没有权限直接读写 `/etc/shadow`。但普通用户又必须能修改自己的密码，所以 passwd 设置 SUID。

当属主执行权限不存在但 SUID 存在时，权限位通常显示为大写 `S`：

```text
-rwSr-xr-x root root program
```

## SGID：继承属组或以文件属组身份执行

### SGID 文件

对可执行文件设置 SGID 后，程序执行时的有效组通常会变成文件属组：

```bash
chmod 2755 program
ls -l program
# -rwxr-sr-x root app program
```

SGID 文件的风险模型与 SUID 类似，但影响范围是组权限。应重点检查文件属组是否可信，以及程序是否会使用该组权限读取、写入或执行文件。

### SGID 目录

对目录设置 SGID 后，在目录中创建的普通文件和子目录通常继承该目录的属组。这适合团队共享目录：

```bash
sudo groupadd app-team
sudo mkdir -p /srv/project
sudo chown root:app-team /srv/project
sudo chmod 2770 /srv/project
```

加入 `app-team` 的用户在 `/srv/project` 中创建文件时，文件属组通常会继承为 `app-team`，而不是创建者的主组。还应结合 `umask` 或默认 ACL 控制新文件是否对组可读写。

查看目录的 SGID：

```bash
stat -c '%A %a %U:%G %n' /srv/project
# drwxrws--- 2770 root:app-team /srv/project
```

在某些文件系统、程序显式设置属组或用户没有相应组权限的情况下，具体继承结果可能受内核和应用行为影响，应以实际 `stat` 结果为准。

## Sticky：目录粘滞位

Sticky 位最常用于多人可写目录，例如 `/tmp`：

```text
drwxrwxrwt root root /tmp
```

目录拥有写权限时，用户可以创建、删除和重命名目录项。设置 Sticky 位后，删除或重命名文件还需要满足以下条件之一：

- 操作者是该文件的属主。
- 操作者是该目录的属主。
- 操作者是 `root` 或拥有相应管理权限。

Sticky 位不限制文件内容读取和写入，也不会赋予用户任何提权能力。它只是限制共享目录中的删除和重命名。

创建实验目录：

```bash
mkdir shared
chmod 1777 shared
stat -c '%A %a %U:%G %n' shared
```

如果目录设置了 Sticky 位但没有其他用户的执行权限，权限字符串中的末尾可能显示大写 `T`。查找“其他用户可写但没有 Sticky 位”的目录：

```bash
sudo find / -xdev -type d -perm -0002 ! -perm -1000 -ls 2>/dev/null
```

这类目录需要重点结合业务用途排查：如果高权限服务会在其中加载脚本、插件、配置或临时文件，可能形成文件替换、符号链接攻击或代码执行风险。Sticky 位不能替代安全的临时文件创建方式，例如 `mkstemp` 或 `O_CREAT|O_EXCL`。
