- [vrrp协议](#vrrp协议)
  - [vrrp状态](#vrrp状态)
  - [vrrp选举机制](#vrrp选举机制)

# vrrp协议

vrrp（Virtual Router Redundancy Protocol）虚拟路由容器协议，是一种通过共享虚拟路由器ip地址，实现了路由器的冗余备份，避免了路由器单点故障导致网络中断的情况。

vrrp提供的是本地冗余，需要主备主备路由器在同一个子网中。

## vrrp状态

VRRP 状态机中定义了三种状态：Initialize、Master 和 Backup。只有处于 Master 状态的设备才能转发发往虚拟 IP 地址的数据包。

|    状态    |                                                             描述                                                              |
| :--------: | :---------------------------------------------------------------------------------------------------------------------------: |
| Initialize |                                 VRRP 不可用。处于 Initialize 状态的设备不处理 VRRP 通告报文。                                 |
|   Master   |                 Master 状态的 VRRP 设备接管虚拟路由设备的所有转发任务，并定期向虚拟路由器发送 VRRP 通告报文。                 |
|   Backup   | 处于Backup状态的VRRP设备不接管虚拟路由设备的转发任务，周期性地接收来自Master设备的VRRP 通告报文，判断Master设备是否正常工作。 |

## vrrp选举机制

VRRP组创建后，设备根据配置的优先级选举主设备，优先级高的选为Master，当Master宕机后，Backup会进行下一次选举。
