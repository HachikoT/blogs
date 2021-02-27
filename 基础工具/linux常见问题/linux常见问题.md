# 设置时区

如果你使用的 Linux 系统使用 Systemd，还可以使用 timedatectl 命令来更改 Linux 系统范围的时区。在 Systemd 下有一个名为 systemd-timedated 的系统服务负责调整系统时钟和时区，我们可以使用 timedatectl 命令对此系统服务进行配置。

```sh
sudo timedatectl set-timezone 'Asia/Shanghai'
```

# ubuntu下vi方向键无法使用

ubuntu（包括树莓派的系统）中vi在编辑状态下方向键不能用，还有回格键不能删除等我们平时习惯的一些键都不能使用。这是因为ubuntu预装的是vim tiny版本，这里需要切换为vim full版本：

```sh
sudo apt remove -y vim-common
sudo apt install -y vim
```

# 参考资料

- [如何调整Linux系统为正确时区](https://www.linuxprobe.com/linux-time.html)
