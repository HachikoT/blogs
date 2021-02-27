# 设置时区

如果你使用的 Linux 系统使用 Systemd，还可以使用 timedatectl 命令来更改 Linux 系统范围的时区。在 Systemd 下有一个名为 systemd-timedated 的系统服务负责调整系统时钟和时区，我们可以使用 timedatectl 命令对此系统服务进行配置。

```sh
sudo timedatectl set-timezone 'Asia/Shanghai'
```

# 参考资料

- [如何调整Linux系统为正确时区](https://www.linuxprobe.com/linux-time.html)
