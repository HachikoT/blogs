# k8s简介

![k8s-logo](k8s-logo.jpg)

k8s全称kubernetes，kubernetes源于希腊语，意为“舵手”或“领航员”。是容器编排与管理的平台。

# k8s架构

![k8s-arch](k8s-arch.png)

一般将控制组件部署在master节点上，其余的node是工作节点，用来运行pod。
各个控制面板组件功能如下：

- **kube-apiserver**：提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
- **kube-controller-manager**：负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
- **kube-scheduler**：负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
- **etcd**：分布式键值存储系统，用于保存集群状态数据。

各个Node组件如下：

- **kubelet**：负责维护容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
- **kube-proxy**：负责为Service提供cluster内部的服务发现和负载均衡；

# 参考资料

- [Kubernetes 文档](https://kubernetes.io/zh/docs/home/)
- [k8s](https://www.cnblogs.com/xy51/p/12744956.html)
