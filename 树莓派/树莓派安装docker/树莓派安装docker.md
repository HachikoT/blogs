# 树莓派安装docker

下载`get.docker.com`脚本，并执行：

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

将用户添加`docker`组：

```sh
# 将用户添加到docker用户组
sudo gpasswd -a ${USER} docker
# 重启docker
sudo systemctl restart docker
# 更新用户组（或者重新开一个窗口）
newgrp docker 
```

# 参考资料

- [Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script)
