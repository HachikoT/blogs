- [安装golang](#安装golang)
  - [方法一，安装包安装](#方法一安装包安装)
  - [方法二，yum安装](#方法二yum安装)
- [参考资料](#参考资料)

# 安装golang

## 方法一，安装包安装

登录[Download Golang](https://golang.google.cn/dl/)，选择合适的安装包，这里我的操作系统是linux，芯片是amd64的，然后选择下载`go1.16.10`。

![golang-downloads](golang-downloads.png)

这里直接用命令行下载到centos7主机中。

```bash
wget https://golang.google.cn/dl/go1.16.10.linux-amd64.tar.gz
```

解压安装包到`/usr/local`目录下。

```bash
sudo tar -C /usr/local -xzf go1.16.10.linux-amd64.tar.gz
```

设置环境变量`vi ~/.bash_profile`，或者修改`/etc/profile`。

```bash
export GOROOT=/usr/local/go
export GOPATH=~/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

重新登录刷新环境变量，测试`go version`，查看版本信息是否正确。

```bash
[rc@localhost ~]$ go version
go version go1.16.10 linux/amd64
```

创建`GOPATH`目录还不存在也没关系，后面需要的时候go会自动帮你创建的。

## 方法二，yum安装

首先需要提前安装好`epel`源，centos7自带的源里面没有go的安装包，`epel`（Extra Packages for Enterprise Linux）是由Fedora社区打造，为RHEL及衍生发行版如CentOS等提供高质量软件包的项目，里面包含了许多基本源里没有的软件源，其中就包括go。

```bash
sudo yum install -y epel-release
```

通过`yum`安装golang。

```bash
sudo yum install -y golang
```

安装好之后，通过`go env`命令查看环境变量设置，找到`GOROOT`，`GOPATH`这两个，看值是什么。

```bash
[rc@localhost ~]$ go env | grep -E "(GOROOT|GOPATH)"
GOPATH="/home/rc/go"
GOROOT="/usr/lib/golang"
```

然后手动设置环境变量`vi ~/.bash_profile`，或者修改`/etc/profile`。

```bash
export GOROOT=/usr/lib/golang
export GOPATH=~/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

# 参考资料

- [Download Golang](https://golang.google.cn/dl/)
- [Install Golang](https://golang.google.cn/doc/install)
