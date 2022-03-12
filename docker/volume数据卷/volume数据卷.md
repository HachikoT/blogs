- [数据卷（volume）](#数据卷volume)
- [匿名卷（anonymous volume）](#匿名卷anonymous-volume)
- [参考资料](#参考资料)

# 数据卷（volume）

由于容器被删除后，其可写的容器层文件并不会保留下来，为了持久化容器的存储，docker提出了数据卷的概念，通过创建数据卷，然后挂载到容器中，从而将容器中的需要持久化存储的数据保存到宿主机上，并且由docker管理数据目录，不用担心具体使用宿主机的哪个目录来进行挂载。
创建数据卷。

```bash
docker volume create mysql-server-volume
```

挂载刚才创建的数据卷，这里用mysql镜像作为示例。

```bash
docker run -d --name mysql-server -v mysql-server-volume:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7.32
```

此时，mysql数据库的数据都已经存储到数据卷中了，要想看`mysql-server-volume`数据卷在宿主机的位置，可以用`docker inspect containerID`，里面`Mounts`字段有相关的信息，其中`Source`字段就是数据卷在宿主机的目录。

```bash
        "Mounts": [
            {
                "Type": "volume",
                "Name": "mysql-server-volume",
                "Source": "/var/lib/docker/volumes/mysql-server-volume/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
```

# 匿名卷（anonymous volume）

为了防止运行时用户忘记将动态文件所保存目录挂载为数据卷，在Dockerfile中，可以事先指定某些目录挂载为匿名卷，这样在运行时如果用户不指定挂载，那么docker会为其创建匿名卷（anonymous volume）。

```bash
VOLUME /data
```

这里的`/data`目录就会在容器运行时自动挂载为匿名卷，当然，运行容器时可以覆盖这个挂载设置。比如：

```bash
docker run -d -v /host-data:/data xxxx
```

比如mysql的Dockerfile中就指定了匿名卷。

```dockerfile
VOLUME /var/lib/mysql
```

所以直接运行mysql容器会自动创建匿名卷。

```bash
docker run -d --name mysql-server -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7.32
```

查看刚创建的匿名卷。

```bash
[rc@localhost ~]$ docker volume ls
DRIVER    VOLUME NAME
local     5bdd98b079c49da27c2ac457ca593b3fe87b4c6c9742051c094ea18e29b061a5
```

# 参考资料

- [Persist the DB](https://docs.docker.com/get-started/05_persisting_data/)
- [Docker匿名卷](https://zhuanlan.zhihu.com/p/38701781)
- [mysql](https://registry.hub.docker.com/_/mysql)
