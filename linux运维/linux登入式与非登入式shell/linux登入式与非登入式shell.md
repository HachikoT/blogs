- [登入相关配置文件](#登入相关配置文件)
- [登入式shell与非登入式的区别](#登入式shell与非登入式的区别)
- [登入式与非登入式shell的开启方式](#登入式与非登入式shell的开启方式)
- [参考资料](#参考资料)

# 登入相关配置文件

拿centos7环境举例，bash相关的配置文件主要分为全局配置文件和个人配置文件。

- **全局配置文件**
  - `/etc/profile`
  - `/etc/profile.d/*.sh`
  - `/etc/bashrc`
- **个人配置文件**
  - `~/.bash_profile`
  - `~/.bashrc`

可以看到，配置文件主要分为`profile`类和`bashrc类文件`。

- `profile`类文件主要作用
  1. 设定环境变量。
  2. 运行命令或脚本（登录时运行的脚本）。
- `bashrc`类文件主要作用
  1. 设定本地变量。
  2. 定义命令别名。

# 登入式shell与非登入式的区别

区别在于，初始化shell时执行的初始化步骤不同，读取配置文件的顺序不同。

- **登入式shell读取配置流程**
  - `/etc/profile`->`/etc/profile.d/*.sh`->`~/.bash_profile`->`~/.bashrc`->`/etc/bashrc`
- **非登入式shell读取配置流程**
  - `~/.bashrc`->`/etc/bashrc`->`/etc/profile.d/*.sh`

在`profile`类的文件中会设置`HOME`，`USER`，`PATH`，`MAIL`等环境变量，所以非登陆shell可能会面临奇怪的命令找不到，环境变量未定义等问题。

# 登入式与非登入式shell的开启方式

- **登录式shell**
  - 正常通过某终端登录的shell。
  - `su - username`方式切换用户。
  - `su -l username`方式切换用户。
- **非登录式shell**
  - `su username`方式切换用户。
  - 图形终端下打开的命令窗口。
  - 自动执行的shell脚本。

所以一般切换用户的时候，建议使用`su -`方式而不是`su`这种方式。

# 参考资料

- [linux之登录式shell和非登录式shell](https://blog.csdn.net/weixin_37335761/article/details/124532460)
- [Linux中su和su -区别，别再傻傻分不清](https://www.bbsmax.com/A/n2d9qVy4dD/)
