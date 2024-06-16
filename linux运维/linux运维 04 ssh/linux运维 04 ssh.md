- [ssh](#ssh)
  - [sshd的监听端口](#sshd的监听端口)
  - [ssh登陆root，密码被拒绝](#ssh登陆root密码被拒绝)
  - [ssh服务器指纹](#ssh服务器指纹)
  - [ssh密钥登陆](#ssh密钥登陆)
- [参考资料](#参考资料)

# ssh

SSH（Secure Shell）是一种网络协议，用于加密两台计算机之间的通信，并且支持各种身份验证机制。

SSH的软件架构是C/S模式。在这个架构中，SSH软件分成两个部分：向服务器发出请求的部分，称为客户端（client），OpenSSH的实现为ssh；接收客户端发出的请求的部分，称为服务器（server），OpenSSH的实现为sshd。

## sshd的监听端口

可以通过设置sshd的配置文件（`/etc/ssh/sshd_config`）中的`Port`字段来修改sshd服务监听端口。ssh协议默认监听端口是22。

也可以直接通过命令查看当前监听端口

```sh
[root@centos ~]# sudo ss -lntp | grep ssh
LISTEN     0      128          *:22                       *:*                   users:(("sshd",pid=7789,fd=3))
LISTEN     0      128       [::]:22                    [::]:*                   users:(("sshd",pid=7789,fd=4))
```

## ssh登陆root，密码被拒绝

可能是sshd的配置文件（`/etc/ssh/sshd_config`）中的`PermitRootLogin`字段设置为`no`，导致root用户无法通过ssh登陆。

可以手动修改sshd的配置文件（`/etc/ssh/sshd_config`），注意不要修改错了，因为客户端的配置文件是`/etc/ssh/ssh_config`，容易改错。

```sh
...

#LoginGraceTime 2m
PermitRootLogin no
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

...
```

然后再重启sshd服务，就可以生效了。

```sh
sudo systemctl restart sshd
```

## ssh服务器指纹

ssh 会将本机连接过的所有服务器公钥的指纹，都储存在本机的`~/.ssh/known_hosts`文件中。每次连接服务器时，通过该文件判断是否为陌生主机（陌生公钥）。

服务器指纹可以防止有人恶意冒充远程主机。如果服务器的密钥发生变更（比如重装了 SSH 服务器），客户端再次连接时，就会发生公钥指纹不吻合的情况。这时，客户端就会中断连接，并显示一段警告信息。这个时候可以手动删除`known_hosts`中旧的指纹。

查看服务器的rsa公钥的指纹：

```sh
[root@centos ~]# ssh-keygen -l -f /etc/ssh/ssh_host_rsa_key.pub 
2048 SHA256:0bBXVvStUFmZmXGCsoKJLtuFAHeVBDhssFTMIzl4+RA no comment (RSA)
```

## ssh密钥登陆

OpenSSH提供了一个工具程序`ssh-keygen`命令，用来生成密钥，默认生成rsa密钥和公钥，保存为`~/.ssh/id_rsa`和`~/.ssh/id_rsa.pub`：

```sh
ssh-keygen
```

生成密钥以后，公钥必须上传到服务器，才能使用公钥登录。OpenSSH规定，用户公钥保存在服务器的`~/.ssh/authorized_keys`文件。你要以哪个用户的身份登录到服务器，密钥就必须保存在该用户主目录的`~/.ssh/authorized_keys`文件。

OpenSSH自带一个`ssh-copy-id`命令，可以自动将公钥添加到远程服务器的`~/.ssh/authorized_keys`文件末尾。如果`~/.ssh/authorized_keys`文件不存在，`ssh-copy-id`命令会自动创建该文件。

```sh
ssh-copy-id -i key_file user@host
```

# 参考资料

- [SSH 教程](https://wangdoc.com/ssh/index.html)
