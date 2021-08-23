# docker简介

![docker-logo](Moby-logo.png)

**docker是一种轻量级的虚拟化技术，可以将你的应用程序及其依赖的运行环境一起打包成一个镜像发布，使得其在任何地方都能获得相同的运行环境。与传统的VM相比，它更轻量，启动更快，单台机器上可以跑成百上千的容器。**
docker诞生于2013年初，最初是dotCloud公司内部的一个业余项目。它基于Google公司推出的Go语言实现。项目后来加入了Linux基金会，遵从了Apache 2.0协议，项目代码在GitHub上进行维护。docker自开源后受到广泛的关注和讨论，以至于dotCloud公司后来都改名为Docker Inc。docker从17.03版本之后分为CE（Community Edition）和EE（Enterprise Edition）。

# 安装docker

- **首先移除旧版本（旧版本叫docker或者docker-engine，新版本叫docker-ce），若第一次安装docker可以跳过该步骤：**

```sh
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

- **安装依赖包：**

```sh
sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
```

- **添加docker仓库：**

```sh
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

- **安装docker ce：**

```sh
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

- **设置国内镜像源：`sudo vi /etc/docker/daemon.json`**

这里应该要等docker服务启动后才有`/etc/docker`目录，所以可以先启动docker服务，再来修改这个镜像源，然后重启docker服务

```json
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

- **开启docker服务：**

```sh
# 将用户添加到docker用户组
sudo gpasswd -a ${USER} docker

# 开机自启动Docker服务
sudo systemctl enable docker

# 开启Docker服务
sudo systemctl start docker
```

- **运行hello-world测试docker是否能正常运行：**

```sh
docker run --rm hello-world
```

以下是正常运行运行结果：

```sh
[rc@cos101]~$ docker run --rm hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

# 安装docker-compose

docker-compose是用于定义和运行多容器docker应用程序的工具。通过docker-compose，您可以使用yaml文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从yaml文件配置中创建并启动所有服务。
在安装docker-compose之前先添加EPEL源，安装命令`sudo yum install -y epel-release`，然后就可以直接安装docker-compose了：

```sh
sudo yum install -y docker-compose
```

# 参考资料

- docker官网安装步骤：[Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)
