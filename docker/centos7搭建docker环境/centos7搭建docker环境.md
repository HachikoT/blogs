- [安装docker](#安装docker)
  - [卸载旧版本](#卸载旧版本)
  - [添加yum仓库](#添加yum仓库)
  - [安装](#安装)
  - [启动服务](#启动服务)
  - [将当前用户添加到docker用户组](#将当前用户添加到docker用户组)
  - [修改镜像源](#修改镜像源)
  - [hello-world测试](#hello-world测试)
- [安装docker-compose](#安装docker-compose)
- [参考资料](#参考资料)

# 安装docker

## 卸载旧版本

旧版本的docker叫做`docker`或者`docker-engine`，如果之前安装过需要先卸载，避免冲突。没安装过可以跳过这一步。

```bash
sudo yum remove docker \
                 docker-client \
                 docker-client-latest \
                 docker-common \
                 docker-latest \
                 docker-latest-logrotate \
                 docker-logrotate \
                 docker-engine
```

## 添加yum仓库

首先安装`yum-utils`，里面提供`yum-config-manager`工具，可以帮助管理yum仓库，然后添加docker的稳定版本源。

```bash
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

## 安装

安装docker，新版本的docker叫做`docker-ce`，并且安装`containerd`。

```bash
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

## 启动服务

设置开机自启动docker服务。

```bash
sudo systemctl enable docker.service
```

启动docker服务。

```bash
sudo systemctl start docker
```

## 将当前用户添加到docker用户组

```bash
sudo gpasswd -a ${USER} docker
```

## 修改镜像源

`sudo vi /etc/docker/daemon.json`，添加镜像源配置，这里设置为docker官方中国区源。

```bash
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

重启docker服务，更新配置。

```bash
sudo systemctl restart docker
```

## hello-world测试

运行hello-world镜像测试docker是否能正常运行。

```bash
sudo docker run --rm hello-world
```

# 安装docker-compose

`docker-compose`是docker提供的一个命令行工具，用来定义和运行由多个容器组成的应用。使用`docker-compose`我们可以通过yaml文件声明式的定义应用程序的各个服务，并由单个命令完成应用的创建和启动。
在安装`docker-compose`之前先添加EPEL源。

```bash
sudo yum install -y epel-release
```

然后就可以直接安装`docker-compose`了。

```bash
sudo yum install -y docker-compose
```

# 参考资料

- [Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)