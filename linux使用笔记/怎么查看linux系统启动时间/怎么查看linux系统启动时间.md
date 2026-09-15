[toc]

# 怎么查看linux系统启动时间

Linux 系统启动后，内核会记录本次开机的时间。可以通过 `uptime`、`last` 等命令查看系统启动时间，也可以查看系统已经运行了多久。

## 使用uptime命令查看

使用 `uptime -s` 可以直接显示系统最近一次启动的日期和时间：

```bash
rc@ubuntu:~$ uptime -s
2026-09-15 08:23:41
```

需要注意的是，这个启动时间是根据当前日期减去运行时间算出来的，所以中途改了系统时间查询出来的启动时间也会变

```bash
rc@ubuntu:~$ sudo date -s "$(date -d '+1 day' '+%Y-%m-%d %H:%M:%S')"
Wed 16 Sep 2026 09:02:03 AM PDT
rc@ubuntu:~$ date
Wed 16 Sep 2026 09:02:04 AM PDT
rc@ubuntu:~$ uptime -s
2026-09-16 07:57:44            # 启动日期也变了
```

如果还想查看系统已经运行了多长时间，可以直接执行 `uptime`：

```bash
rc@ubuntu:~$ uptime
 09:36:12 up 1:12,  1 user,  load average: 0.08, 0.05, 0.01
```

其中 `up 1:12` 表示系统已经运行了 1 小时 12 分钟。

或者执行`uptime -p`展示

```bash
rc@ubuntu:~$ uptime -p
up 1 hour, 20 minutes
```

## 使用last命令查看重启记录

`last reboot` 可以查看系统历史上的重启记录：

```bash
rc@ubuntu:~$ last reboot
reboot   system boot  5.15.0-139-gener Tue Sep 15 07:57   still running
reboot   system boot  5.15.0-139-gener Sat Sep 12 07:24   still running
reboot   system boot  5.15.0-139-gener Sat Sep 12 07:21 - 07:23  (00:02)
reboot   system boot  5.15.0-139-gener Fri Sep 11 08:11 - 07:23  (23:12)

wtmp begins Fri Sep 11 08:11:39 2026
```

第一条记录通常是当前这次启动，`still running` 表示系统目前仍在运行。`last` 的记录来自 `/var/log/wtmp`，如果日志被清理或系统刚安装不久，历史记录可能不完整。
