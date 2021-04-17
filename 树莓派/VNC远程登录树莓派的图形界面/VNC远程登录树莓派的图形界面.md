# 树莓派安装tightvncserver

首先需要先ssh到树莓派，执行下面的安装命令：

```sh
sudo apt-get install tightvncserver
```

安装好之后请一定先使用此命令设置一个VNC密码：

```sh
vncpasswd
```

启动tightvncserver（这里就不设置开机自启动了，因为我只是偶尔需要登陆图形界面）：

```sh
tightvncserver
```

# windows安装vnc客户端

可以安装[vnc viewer](https://www.realvnc.com/en/connect/download/viewer/)。
登录地址输入“IP地址:控制台号码”，0号控制台可不加号码。

# 参考资料

- [VNC远程登录树莓派的图形界面](https://shumeipai.nxez.com/2013/09/04/login-rpi-with-vnc.html)
- [5款免费Windows远程连接Linux桌面软件（VNC客户端）](https://www.cnblogs.com/lcword/p/5914063.html)
