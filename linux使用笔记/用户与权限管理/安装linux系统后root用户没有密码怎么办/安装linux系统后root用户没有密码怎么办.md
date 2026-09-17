[toc]

# 安装linux系统后root用户没有密码怎么办

很多发行版（尤其是 Ubuntu/Debian）在安装过程中只让你创建一个普通用户，root 账户默认是**锁定状态**（`/etc/shadow` 中密码字段为 `!` 或 `*`），所以装完系统后 root 是“没有密码”的。CentOS/RHEL 则一般是安装时设置过，但如果当时跳过了，或者把密码忘了，也会遇到同样的问题。

## 查看root用户的密码状态

此时，查看root用户的密码状态如下：

```bash
rc@ubuntu:~$ sudo passwd -S root
root L 09/11/2026 0 99999 7 -1
```

输出一共有 7 个字段，用空格分隔，含义如下：

| 字段 | 示例值       | 含义                                                                    |
| ---- | ------------ | ----------------------------------------------------------------------- |
| 1    | `root`       | 用户名                                                                  |
| 2    | `L`          | 密码状态：`P` 表示已设置有效密码，`L` 表示密码被锁定，`NP` 表示没有密码 |
| 3    | `09/11/2026` | 密码上次修改的日期                                                      |
| 4    | `0`          | 密码最短使用天数（改过之后几天内不允许再次修改）                        |
| 5    | `99999`      | 密码最长使用天数（到期必须更换）                                        |
| 6    | `7`          | 密码到期前提前警告的天数                                                |
| 7    | `-1`         | 密码过期后账号被禁用的宽限天数（`-1` 表示不会禁用）                     |

这里状态是 `L`，说明 root 的密码字段在 `/etc/shadow` 中是 `!` 或 `*`，即**被锁定**，无法直接用 root 登录（包括 `su`）。可以确认一下：

```bash
rc@ubuntu:~$ sudo grep '^root:' /etc/shadow
root:!:20707:0:99999:7:::
```

第二个字段只有 `!`（或 `*`），没有密码哈希，所以 root 就是"没有密码"的状态。

## 解决办法：给root设置密码

普通用户有 sudo 权限的话，直接执行 `passwd` 即可：

```bash
rc@ubuntu:~$ sudo passwd root
New password:            # 输入要设置的 root 密码
Retype new password:     # 再输一遍
passwd: password updated successfully
```

设置完成后再看状态，`L` 变成了 `P`：

```bash
rc@ubuntu:~$ sudo passwd -S root
root P 09/11/2026 0 99999 7 -1
```

此时 `/etc/shadow` 中 root 的密码字段也变成了真正的哈希值：

```bash
rc@ubuntu:~$ sudo grep '^root:' /etc/shadow
root:$6$xxxx...:20707:0:99999:7:::
```

## 补充说明

- **安全建议**：不建议日常使用 root 直接登录。设置密码主要是为了 `su` 切换和单用户维护（rescue mode）时可用，日常操作继续用 `sudo` 更安全。如果哪天想重新锁回去，执行 `sudo passwd -l root` 即可。

