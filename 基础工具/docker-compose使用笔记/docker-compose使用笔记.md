# docker-compose简介

docker-compose是docker官方的开源项目，负责实现对docker容器集群的快速编排，通过一个单独的yaml配置文件，来定义一组相关的容器来为一个项目服务。使容器的操作能够批量地，可视地执行，是一个管理多个容器的工具，比如可以解决容器之间的依赖关系。docker-compose将所管理的容器分为三层，分别是工程（project），服务（service）以及容器（container）。

# docker-compose命令

docker-compose命令格式：

```sh
docker-compose [-f <arg>...] [options] [--] [COMMAND] [ARGS...]
```

选项说明：

- **-f**：指定docker-compose模板文件，默认为docker-compose.yml。
- **-p**：指定项目名称，默认使用当前所在目录为项目名。
