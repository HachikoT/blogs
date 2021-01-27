# docker-compose简介

![docker-compose-logo](docker-compose-logo.jpg)

docker-compose是docker官方的开源项目，负责实现对docker容器集群的快速编排，通过一个单独的yaml配置文件，来定义一组相关的容器来为一个项目服务。使容器的操作能够批量地，可视地执行，是一个管理多个容器的工具，比如可以解决容器之间的依赖关系。docker-compose将所管理的容器分为三层，分别是工程（project），服务（service）以及容器（container）。

# docker-compose命令

docker-compose命令格式：

```sh
docker-compose [-f <arg>...] [options] [--] [COMMAND] [ARGS...]
```

选项说明：

- **-f**：指定docker-compose模板文件，默认为docker-compose.yml。
- **-p**：指定项目名称，默认使用当前所在目录为项目名。

`docker-compose up`创建和开启容器、网络、卷、镜像：

```sh
up [options] [--scale SERVICE=NUM...] [--] [SERVICE...]
```

选项说明：

- **-d**：在后台运行服务容器。

`docker-compose down`停止和删除容器、网络、卷、镜像：

```sh
down [options]
```

# docker-compose.yaml模板文件

docker-compose允许用户通过一个docker-compose.yml模板文件来定义一组相关联的应用容器为一个项目（project）。docker-compose标准模板文件应该包含version、services、networks三大部分，最关键的是services和networks两个部分。

- **version**：

docker-compose目前有三个版本分别为version 1，version 2，version 3，表示支持的功能版本，一般可以写为version 3：

```yaml
version: '3'
```

- **image**：

指定服务的镜像名称或镜像ID。如果镜像在本地不存在，docker-compose将会尝试拉取镜像。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
```

- **container_name**：

指定容器名。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
```

- **stdin_open**：

打开stdin，和`docker run -i`一样。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
```

- **tty**：

分配一个伪终端，和`docker run -t`一样。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
```

- **working_dir**：

设置工作目录。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
    working_dir: /
```

- **ports**：

指定端口映射，使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
    working_dir: /
    ports: 
    - "9000"
    - "8080:8080"
```

- **volumes**：

挂载一个目录或者一个已存在的数据卷容器，可以直接使用[HOST:CONTAINER]格式，或者使用[HOST:CONTAINER:ro]格式，后者对于容器来说，数据卷是只读的，可以有效保护宿主机的文件系统。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
    working_dir: /
    ports: 
    - "9000"
    - "8080:8080"
    volumes: 
    - /home:/home
```

- **net**：

设置网络模式。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
    working_dir: /
    ports: 
    - "9000"
    - "8080:8080"
    volumes: 
    - /home:/home
    net: bridge
```

- **command**：

设置容器的执行命令。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
    stdin_open: true
    tty: true
    working_dir: /
    ports: 
    - "9000"
    - "8080:8080"
    volumes: 
    - /home:/home
    net: bridge
    commands: bash
```

- **depends_on**：

设置服务的启动和关闭的依赖顺序。

```yaml
version: '3'
services: 
  app: 
    image: centos:7
    container_name: test
  app2: 
    depends_on: 
      - app
```

- **extend**：

基于已有的服务进行扩展。例如我们已经有了一个`webapp`服务，模板文件为`common.yml`。

```yaml
# common.yml
webapp: 
  build: ./webapp
  environment:
    - DEBUG=false
    - SEND_EMAILS=false
```

编写一个新的`development.yml`文件，使用`common.yml`中的`webapp`服务进行扩展。

```yaml
# development.yml
web: 
  extends: 
    file: common.yml
    service: webapp
  ports: 
    - "8000:8000"
  links: 
    - db
  environment: 
    - DEBUG=true
```
