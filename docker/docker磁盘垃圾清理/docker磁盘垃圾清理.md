- [docker资源占用情况](#docker资源占用情况)
- [镜像资源清理](#镜像资源清理)
- [容器资源清理](#容器资源清理)
- [数据卷和网络资源清理](#数据卷和网络资源清理)
- [构建缓存清理](#构建缓存清理)
- [参考资料](#参考资料)

# docker资源占用情况

docker用久之后，会遇到磁盘占用越来越大的情况。这个时候可以用`docker system df`命令来查看docker的资源占用情况。

```bash
[rc@localhost ~]$ docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          2         1         448.5MB   13.26kB (0%)
Containers      1         0         68B       68B (100%)
Local Volumes   1         1         219.5MB   0B (0%)
Build Cache     0         0         0B        0B
```

这里分别列出了docker中不同资源类型所占用的磁盘空间，其中的`Build Cache`表示平时构建镜像过程中产生的缓存数据，`RECLAIMABLE`表示当前可以回收的空间大小。

# 镜像资源清理

`docker image prune`命令可以清理所有标签为`none`的镜像。

```bash
[rc@localhost ~]$ docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N]
```

也可以手动写出删除的条件，不过这样命令会长一些，比较难记。

```bash
docker rmi $(docker images -f dangling=true -q)
```

# 容器资源清理

`docker container prune`命令可以清理所有停止运行的容器。

```bash
[rc@localhost ~]$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N]
```

也可以手动写出删除的条件，不过这样命令会长一些，比较难记。

```bash
docker rm $(docker ps -f status=exited -q)
```

# 数据卷和网络资源清理

数据卷资源清理。

```bash
[rc@localhost ~]$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N]
```

网络资源清理。

```bash
[rc@localhost ~]$ docker network prune
WARNING! This will remove all custom networks not used by at least one container.
Are you sure you want to continue? [y/N]
```

# 构建缓存清理

docker用久了，`Build Cache`那一项可能占据很大的空间，这时候可以用来清理无用的构建缓存。

```sh
[rc@localhost ~]$ docker builder prune
WARNING! This will remove all dangling build cache. Are you sure you want to continue? [y/N]
```

# 参考资料

- [清理 Docker 占用的磁盘空间](https://www.cnblogs.com/wuxingwang/p/12170071.html)
