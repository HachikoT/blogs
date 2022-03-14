- [用户与组的配置文件](#用户与组的配置文件)
  - [用户配置信息](#用户配置信息)
  - [组配置信息](#组配置信息)
  - [用户密码配置](#用户密码配置)
- [用户管理](#用户管理)
  - [添加用户](#添加用户)
- [参考资料](#参考资料)

# 用户与组的配置文件

- `/etc/passwd`：用户配置信息。
- `/etc/group`：组配置信息。
- `/etc/shadow`：用户密码配置（密码密文保存）。
- `/etc/gshadow`：组密码配置（密码密文保存）。

## 用户配置信息

```bash
rc@rc-virtual-machine:/home$ cat /etc/passwd
rc:x:1000:1000:rc,,,:/home/rc:/bin/bash
```

| 用户名 | 密码（实际存放在`/etc/shadow`中） | UID | GID | 全名或注释 | 家目录 | 默认使用的shell |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| rc | x | 1000 | 1000 | `rc,,,` | `/home/rc` | `/bin/bash` |

## 组配置信息

```bash
rc@rc-virtual-machine:/home$ cat /etc/group
rc:x:1000:
```

| 组名 | 密码（实际存放在`/etc/gshadow`中） | GID | 附加用户 |
| :--: | :--: | :--: | :--: |
| rc | x | 1000 |  |

## 用户密码配置

```bash
rc@rc-virtual-machine:/home$ sudo cat /etc/shadow
[sudo] rc 的密码：
rc:$6$qoOaCPosRISLl4Av$DRf6V5ABSQjFR3gJAOV6exJfNgiu.Y8dqg7yMJLZYaB4ftZrTXmv2.RYbHakWbRCRHl1UggjSwzNLHXS15UU10:18959:0:99999:7:::
```

| 用户名 | 加密密码 | 上一次更改密码的unix时间戳 | 再过几天可以更改（0表示随时可改） | 再过几天必须更改（99999表示永久） | 过期前几天提醒用户 | 过期多久账户将被锁定 | 多少天后账户失效 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| rc | `$6$qoOaCPosRISLl4Av$DRf6V5ABSQjFR3gJAOV6exJfNgiu.Y8dqg7yMJLZYaB4ftZrTXmv2.RYbHakWbRCRHl1UggjSwzNLHXS15UU10` | 18959 | 0 | 99999 | 7 |  |  |

# 用户管理

## 添加用户

通过`useradd`命令可以新增用户。

```bash
useradd [选项] 登录名
```

主要选项如下。

- `-u`：指定UID（系统默认递增）。
- `-g`：所属主组（默认创建同名的主组）。
- `-G`：所属附加组，逗号分隔。
- `-d`：家目录（默认/home/username）。
- `-m`：家目录不存在则创建（一般需要）。
- `-c`：comment，用户描述。
- `-s`：设置用户默认使用的shell。

示例。

```bash
rc@rc-virtual-machine:/home$ sudo useradd -m -d /home/rctest rctest
[sudo] rc 的密码： 
rc@rc-virtual-machine:/home$ ll
总用量 16
drwxr-xr-x  4 root    root    4096 3月  14 12:26 ./
drwxr-xr-x 20 root    root    4096 12月  3 23:07 ../
drwxr-xr-x 31 rc      rc      4096 3月  12 14:46 rc/
drwxr-xr-x  2 rctest  rctest  4096 3月  14 12:26 rctest/
```

# 参考资料

- [Linux中用户与用户组管理](https://www.cnblogs.com/ddz-linux/p/10467106.html)
