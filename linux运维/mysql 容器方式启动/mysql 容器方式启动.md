- [mysql 容器方式启动](#mysql-容器方式启动)

# mysql 容器方式启动

拉取mysql镜像

```bash
docker pull mysql:5.7.32
```

启动mysql容器

```bash
docker run --name mysql --user 1000:1000 -p 3306:3306 -v ~/data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=111111 -d mysql:5.7.32
```

进入mysql容器

```bash
docker exec -it mysql bash
```