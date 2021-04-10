# 树莓派安装mysql容器

在docker hub中，mysql官方的镜像不支持armv7架构，所以使用别人编译上传的镜像：

```sh
docker pull biarms/mysql:5.7.33-beta-circleci
```

接下来运行mysql容器：

```sh
docker run -it -d --name mysql-server -p 3306:3306 -v /var/lib/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=111111 --restart always biarms/mysql:5.7.33-beta-circleci
```

# 参考资料

- [树莓派4B使用docker安装mysql](https://blog.csdn.net/weixin_39875842/article/details/113461578)
