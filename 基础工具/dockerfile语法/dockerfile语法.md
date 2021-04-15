# VOLUME

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

```sh
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```

比如mysql的镜像就设置了VOLUME，所以在运行mysql容器的时候会自动挂载数据目录到宿主机。
那么怎么自动删除掉这些volume呢，可以用`docker rm -v`或者`docker run --rm`这两种方式来自动删除匿名volume。

# 参考资料

- [dockerfile菜鸟教程](https://www.runoob.com/docker/docker-dockerfile.html)