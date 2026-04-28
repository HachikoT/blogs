- [net.ipv4.ip\_forward](#netipv4ip_forward)
- [典型应用场景](#典型应用场景)
- [设置方法](#设置方法)

# net.ipv4.ip_forward

`‌net.ipv4.ip_forward‌`是 Linux 内核中的一个关键参数，用于控制 ‌IPv4 数据包转发功能：

- ‌值为 0（默认）‌：禁用 IP 转发，系统仅处理目标地址为本机的数据包，‌不会‌将从一个网络接口收到的数据包转发到另一个接口。
- 值为 1‌：启用 IP 转发，系统可作为‌路由器或网关‌，在不同网络接口之间转发数据包，实现跨网络通信。

# 典型应用场景

- ‌Docker/Kubernetes 容器网络‌：容器通过桥接网络（如 docker0）访问外网或与其他宿主机通信时，依赖宿主机开启 IP 转发。
- ‌NAT 网络地址转换‌：结合 iptables 实现内网访问公网时，必须开启 IP 转发。

# 设置方法

临时开启（重启失效）

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

永久开启（推荐用于生产环境）‌：

```bash
sudo vi /etc/sysctl.d/99-k8s.conf
# 添加内容
net.ipv4.ip_forward=1
# 使配置生效
sudo sysctl --system
```
