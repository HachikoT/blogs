# registry

Docker官方提供了一个搭建私有仓库的镜像`registry` ，只需把镜像下载下来，运行容器并暴露5000端口，就可以使用了。

```sh
docker run -d -v /opt/registry:/var/lib/registry -p 5000:5000 --restart=always --name my-registry registry
```

这里启动的仓库registry服务不是安全可信赖的。这时需要修改客户端docker的配置文件`/etc/docker/daemon.json`，需要将其地址添加到docker的信任列表中：

```sh
vim /etc/docker/daemon.json
{
    "insecure-registries": ["x.x.x.x:5000"]
}

# 重启docker服务
systemctl restart docker
```

可以用下面的路径查看registry仓库内容：

```html
http://registry:5000/v2/_catalog

http://registry:5000/v2/image_name/tags/list
```

# 参考资料

- [Docker私有仓库Registry实战](https://www.cnblogs.com/gcgc/p/10489385.html)
