- [用户与组的配置文件](#用户与组的配置文件)
  - [用户配置信息](#用户配置信息)
  - [组配置信息](#组配置信息)
  - [用户密码配置](#用户密码配置)
- [用户管理](#用户管理)
  - [添加用户](#添加用户)
  - [设置用户密码](#设置用户密码)
  - [删除用户](#删除用户)
- [组管理](#组管理)
  - [添加组](#添加组)
  - [将已有用户添加到指定组](#将已有用户添加到指定组)
  - [将用户移除出组](#将用户移除出组)
  - [删除组](#删除组)
- [参考资料](#参考资料)

# 用户与组的配置文件

- `/etc/passwd`：用户配置信息。
- `/etc/group`：组配置信息。
- `/etc/shadow`：用户密码配置（密码密文保存）。
- `/etc/gshadow`：组密码配置（密码密文保存）。

## 用户配置信息

```bash
[rc@localhost ~]$ cat /etc/passwd | grep rc
rc:x:1000:1000:rc:/home/rc:/bin/bash
```

| 用户名 | 密码（实际存放在`/etc/shadow`中） | UID | GID | 全名或注释 | 家目录 | 默认使用的shell |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| `rc` | `x` | `1000` | `1000` | `rc` | `/home/rc` | `/bin/bash` |

## 组配置信息

```bash
[rc@localhost ~]$ cat /etc/group | grep rc
wheel:x:10:rc
rc:x:1000:rc
```

| 组名 | 密码（实际存放在`/etc/gshadow`中） | GID | 附加用户 |
| :--: | :--: | :--: | :--: |
| rc | x | 1000 | rc |

## 用户密码配置

```bash
[rc@localhost ~]$ sudo cat /etc/shadow | grep rc
rc:$6$zl9Q14oGB0htw4kt$bURziE3yg8YES8cMesq4t7jm1z88VJI6mJ2Zc13RKbeo0Gj6q2ZHXjEWWO/PTvASvl.gtwKElumvHYfde3Mnz/::0:99999:7:::
```

| 用户名 | 加密密码 | 上一次更改密码的unix时间戳 | 再过几天可以更改（0表示随时可改） | 再过几天必须更改（99999表示永久） | 过期前几天提醒用户 | 过期多久账户将被锁定 | 多少天后账户失效 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| rc | `$6$qoOaCPosRISLl4Av$DRf6V5ABSQjFR3gJAOV6exJfNgiu.Y8dqg7yMJLZYaB4ftZrTXmv2.RYbHakWbRCRHl1UggjSwzNLHXS15UU10` |  | 0 | 99999 | 7 |  |  |

# 用户管理

## 添加用户

通过`useradd`命令可以新增用户。

```bash
useradd [选项] 登录名
```

主要选项如下。

- `-u, --uid`：指定UID（系统默认递增）。
- `-g, --gid`：所属主组（默认创建同名的主组）。
- `-G, --groups`：所属附加组，逗号分隔。
- `-d, --home-dir`：家目录（默认/home/$username）。
- `-m, --create-home`：家目录不存在则创建（一般默认自动创建）。
- `-c, --comment`：用户描述。
- `-s, --shell`：设置用户默认使用的shell。

示例。

```bash
[rc@localhost ~]$ su root
密码：
[root@localhost rc]# useradd sj
[root@localhost rc]# ll /home/
总用量 0
drwx------. 3 rc rc 108 9月  18 17:26 rc
drwx------. 2 sj sj  62 9月  18 23:16 sj
```

## 设置用户密码

用户创建好之后，还需要给它设置好密码。在这之前没法远程登陆该用户，也不能从普通用户切换到该用户。
`passwd`命令用来修改用户的密码。

```bash
passwd [-k] [-l] [-u [-f]] [-d] [-e] [-n mindays] [-x maxdays] [-w warndays] [-i inactivedays] [-S] [--stdin] [username]
```

主要选项。

- `-d, --delete`：删除密码，只有root用户才能切换到没有密码的用户。
- `-e, --expire`：强迫用户下次登录时必须修改密码。
- `-l, --lock`：停止账号使用。
- `-u, --unlock`：启用已被停止的账户。
- `-n, --minimum`：指定密码最短存活期。
- `-x, --maximum`：指定密码最长存活期。
- `-w, --warning`：口令要到期提前警告的天数。
- `-i, --inactive`：口令过期后多少天停用账户。

示例。

```bash
[rc@localhost ~]$ su root
密码：
[root@localhost home]# passwd sj
更改用户 sj 的密码 。
新的 密码：
无效的密码： 密码是一个回文
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[root@localhost home]# cat /etc/shadow | grep sj
sj:$6$Tp8jw7fj$mrwLxbP6xF/smJsMtncjOkLA6y0WuatNYGBADsDxXPwsLIc7dwzYlLktzVhz5XOtDxFeZ863BXKWNz9rd0sbn1:19253:0:99999:7:::
```

## 删除用户

通过`userdel`命令可以删除用户。

```bash
userdel [选项] 登录名
```

主要选项如下。

- `-r, --remove`：用户主目录中的文件将随用户主目录和用户邮箱一起删除。

示例。

```bash
[rc@localhost ~]$ su root
密码：
[root@localhost rc]# userdel -r sj
[root@localhost rc]# cd ..
[root@localhost home]# ll
总用量 0
drwx------. 3 rc rc 108 9月  18 17:26 rc
```

# 组管理

## 添加组

通过`groupadd`命令可以创建一个新组。

```bash
groupadd [选项] 组名
```

示例。

```bash
[rc@localhost home]$ su root
密码：
[root@localhost home]# groupadd good
```

## 将已有用户添加到指定组

通过`usermod`命令可以增加用户的附加组信息。

主要选项。

- `-a, --append`：追加模式，和`-G`一起使用表示添加用户进组。
- `-G, --groups`：附加组列表，和`-a`一起使用，组名用逗号分隔。

示例。

```bash
[rc@localhost home]$ su root
密码：
[root@localhost home]# usermod -a -G good rc
[root@localhost home]# id rc
uid=1000(rc) gid=1000(rc) 组=1000(rc),10(wheel),1002(good)
```

## 将用户移除出组

通过`gpasswd`命令可以增删组成员信息。

```bash
gpasswd [选项] 组名
```

常用选项。

- `-a, --add`：添加一个用户进组。
- `-d, --delete`：将用户从组中删除。
- `-M, --members`：设置组成员列表，逗号分隔。

## 删除组

通过`groupdel`命令可以删除组。

示例。

```bash
[rc@localhost ~]$ su root
密码：
[root@localhost rc]# groupdel good
[root@localhost rc]# id rc
uid=1000(rc) gid=1000(rc) 组=1000(rc),10(wheel)
```

# 参考资料

- [Linux中用户与用户组管理](https://www.cnblogs.com/ddz-linux/p/10467106.html)
- [Linux passwd命令](https://www.runoob.com/linux/linux-comm-passwd.html)
