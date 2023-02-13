- [mysql 容器方式启动](#mysql-容器方式启动)

# mysql 容器方式启动

拉取mysql镜像

```bash
docker pull mysql:5.7.32
```

启动mysql容器

```bash
docker run \
  --name mysql \
  --user 1000:1000 \
  -p 3308:3306 \
  -v /etc/localtime:/etc/localtime:ro \
  -v ~/data/mysql:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=111111 \
  -d mysql:5.7.32
```

- `-v /etc/localtime:/etc/localtime:ro`：确保容器和宿主机的时区一致。
- `-v ~/data/mysql:/var/lib/mysql`：数据库数据文件映射到宿主机。
- `-e MYSQL_ROOT_PASSWORD=111111`：mysql数据库root用户密码。

进入mysql容器

```bash
docker exec -it mysql bash
```