# ssh

SSH（Secure Shell 的缩写）是一种网络协议，用于加密两台计算机之间的通信，并且支持各种身份验证机制。
SSH的软件架构是C/S模式。在这个架构中，SSH软件分成两个部分：向服务器发出请求的部分，称为客户端（client），OpenSSH的实现为ssh；接收客户端发出的请求的部分，称为服务器（server），OpenSSH的实现为sshd。

# 服务器指纹

ssh连接远程服务器后，首先有一个验证过程，验证远程服务器是否为陌生地址（第一次登陆）。如果是第一次连接某一台服务器，命令行会显示一段文字，表示不认识这台机器，提醒用户确认是否需要连接。
所谓“服务器指纹”，指的是 SSH 服务器公钥的哈希值。每台 SSH 服务器都有唯一一对密钥，用于跟客户端通信，其中公钥的哈希值就可以用来识别服务器。
下面的命令可以查看某个公钥的指纹：

```sh
$ ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key.pub
256 SHA256:zDcVqi4DXzj7WhDA7fyWHmgisn39ekHTW8JzfDplRNs no comment (ECDSA)
```

ssh 会将本机连接过的所有服务器公钥的指纹，都储存在本机的`~/.ssh/known_hosts`文件中。每次连接服务器时，通过该文件判断是否为陌生主机（陌生公钥）。
服务器指纹可以防止有人恶意冒充远程主机。如果服务器的密钥发生变更（比如重装了 SSH 服务器），客户端再次连接时，就会发生公钥指纹不吻合的情况。这时，客户端就会中断连接，并显示一段警告信息。这个时候可以手动删除`known_hosts`中旧的指纹。

# 密钥登陆

SSH 默认采用密码登录，这种方法有很多缺点，简单的密码不安全，复杂的密码不容易记忆，每次手动输入也很麻烦。密钥登录是更好的解决方案。
OpenSSH 提供了一个工具程序ssh-keygen命令，用来生成密钥：

```sh
ssh-keygen
```

生成密钥以后，公钥必须上传到服务器，才能使用公钥登录。
OpenSSH 规定，用户公钥保存在服务器的`~/.ssh/authorized_keys`文件。你要以哪个用户的身份登录到服务器，密钥就必须保存在该用户主目录的`~/.ssh/authorized_keys`文件。
OpenSSH 自带一个ssh-copy-id命令，可以自动将公钥拷贝到远程服务器的`~/.ssh/authorized_keys`文件。如果`~/.ssh/authorized_keys`文件不存在，ssh-copy-id命令会自动创建该文件。

# 查看sshd的监听端口

sshd服务的监听端口是根据配置文件来的，默认配置文件为`/etc/ssh/sshd_config`，也可以通过ss命令查看：

```sh
# -p选项可能需要sudo才可以显示出来，查看进程信息
sudo ss -anp | grep ssh
```

# 参考资料

- [SSH 教程](https://wangdoc.com/ssh/index.html)
