# 怎么查看配置的 NTP 服务器，以及自己怎么作为 NTP 服务器

NTP 是 Linux 服务器最基础、最重要的时间同步机制之一。很多系统运行起来后，时间看着正常，但你未必知道它到底在同步哪一个上游服务器；更关键的是，如果需要在内网中统一时间，可能要把某一台机器做成 NTP 服务端，让其他机器统一从它同步。

本文介绍三种常见 Linux 时间同步场景：

| 方案                | 适合场景                   | 典型命令                          | 典型特点                 |
| ------------------- | -------------------------- | --------------------------------- | ------------------------ |
| `systemd-timesyncd` | 轻量服务器、容器、默认系统 | `timedatectl show-timesync --all` | 主要作为客户端，偏轻量   |
| `ntpd`              | 传统 Linux 环境            | `ntpq -p`                         | 稳定，功能完整           |
| `chrony`            | 虚拟机、内网、现代 Linux   | `chronyc tracking`                | 现代化，适合做内网时间源 |

## 怎么看当前机器在用哪个 NTP 服务器

### 查看配置文件

#### systemd-timesyncd

```bash
cat /etc/systemd/timesyncd.conf
```

```bash
[Time]
NTP=ntp.aliyun.com
FallbackNTP=time.windows.com
```

#### ntpd

```bash
cat /etc/ntp.conf
```

```bash
server 1.cn.pool.ntp.org iburst
server ntp.aliyun.com iburst
```

#### chrony

```bash
cat /etc/chrony/chrony.conf
```

```bash
server ntp.aliyun.com iburst
server 1.cn.pool.ntp.org iburst
```

### 命令查看

#### systemd-timesyncd

```bash
rc@ubuntu:~$ timedatectl show-timesync --all
LinkNTPServers=
SystemNTPServers=
FallbackNTPServers=ntp.ubuntu.com
ServerName=ntp.ubuntu.com
ServerAddress=185.125.190.56
RootDistanceMaxUSec=5s
PollIntervalMinUSec=32s
PollIntervalMaxUSec=34min 8s
PollIntervalUSec=34min 8s
NTPMessage={ Leap=0, Version=4, Mode=4, Stratum=2, Precision=-25, RootDelay=6.469ms, RootDispersion=244us, Reference=1D586304, OriginateTimestamp=Mon 2026-09-21 00:14:41 CST, ReceiveTimestamp=Mon 2026-09-21 00:14:41 CST, TransmitTimestamp=Mon 2026-09-21 00:14:41 CST, DestinationTimestamp=Mon 2026-09-21 00:14:41 CST, Ignored=no PacketCount=7, Jitter=12.266ms }
Frequency=-830139
```

#### ntpd

```bash
ntpq -p
```

```bash
     remote           refid      st t when poll reach   delay  offset    jitter
===============================================================================
*ntp.aliyun.com  10.0.0.1     3 u   25   64  377   14.23   0.456   1.234
```

其中前面的 `*` 表示当前在用的同步源。

#### chrony

```bash
chronyc sources -v
```

输出中带有 `^*`、`^+` 这类标记的那一项，通常是当前有效的时间源。

也可以看：

```bash
chronyc tracking
```

## 把本机作为 NTP 服务端（systemd-timesyncd 作为客户端）

`systemd-timesyncd` 本身不是标准的 NTP 服务端程序，通常更适合作为客户端。

如果你想让本机提供 NTP 服务，通常要改成：

- `ntpd`
- `chrony`

其中更推荐 `chrony`。

也就是说：`systemd-timesyncd` 适合“同步上游时间”，不适合直接对外提供 NTP 服务。

### `ntpd` 场景

编辑配置文件

```bash
sudo vi /etc/ntp.conf
```

添加或修改：

```bash
# 允许内网访问
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
restrict 10.0.0.0 mask 255.0.0.0 nomodify notrap

# 上游时间源
server ntp.aliyun.com iburst
server 1.cn.pool.ntp.org iburst

# 允许本机作为时间源
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

重启服务

```bash
sudo systemctl restart ntp
sudo systemctl enable ntp
sudo systemctl status ntp
```

客户端同步到这台机器

在目标机器上配置：

```bash
sudo vi /etc/ntp.conf
```

添加：

```bash
server 192.168.1.10 iburst
```

然后：

```bash
sudo systemctl restart ntp
ntpq -p
```

### `chrony` 场景

编辑配置：

```bash
sudo vi /etc/chrony/chrony.conf
```

建议添加：

```bash
# 允许内网访问
allow 192.168.1.0/24
allow 10.0.0.0/8

# 本机作为局域网时间源
local stratum 10

# 上游同步源
server ntp.aliyun.com iburst
server 1.cn.pool.ntp.org iburst
```

重启并启用：

```bash
sudo systemctl restart chrony
sudo systemctl enable chrony
sudo systemctl status chrony
```

客户端同步到这台机器

在其他机器上，配置：

```bash
sudo vi /etc/chrony/chrony.conf
```

添加：

```text
server 192.168.1.10 iburst
```

然后重启：

```bash
sudo systemctl restart chrony
chronyc tracking
```