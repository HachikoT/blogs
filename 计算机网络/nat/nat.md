- [nat](#nat)
  - [基本nat和napt](#基本nat和napt)
  - [nat和tcp](#nat和tcp)
  - [nat和udp](#nat和udp)
  - [nat和icmp](#nat和icmp)

# nat

nat（network address translation）网络地址转换技术主要用于将多个私有ip映射为少量的公有ip，这个映射对于网络流量来说是双向的，数据流出的时候修改源地址，流入的时候再将目的地址改回来。以此来降低公有ip地址短缺的问题。

## 基本nat和napt

基本nat（network address translation）只执行ip地址的转换，本质上就是将私有ip地址转换为公有ip地址，这个ip一般是一个ip池。这种nat一般不怎么用，因为每一个私有ip都需要一个公有ip，只是对活跃的私有ip很少，关机的私有ip比较多的情况下能减缓地址短缺的问题。

还有比较常用的就是napt（network address port translation）会同时转换ip地址和端口，这里的端口对于tcp协议和udp协议而言就是它们的端口，对于icmp协议而言就是它的查询标识符。napt往往只需要配置一个公有ip就可以了，对于内部网络的出口流量，会将其ip和端口映射到自己公有ip和随机端口上。

## nat和tcp

在收到内部主机的tcp连接请求包SYN包时，nat会对其作地址转换，并且创建一个nat会话（nat session）来记录这个tcp连接。然后对这个会话的状态进行维护。

1. nat检测到一个传出的SYN包后，会激活连接计时器（connection timer），如果计时器到期还没收到ack，那么就清除这个nat会话。
2. 如果ack包到达了，那么清除连接计时器，并创建一个超时较长的会话计时器（session timer），并且可以配置隔一段时间给内部主机发探测消息，如果收到ack那么重置计时器，如果没收到那么超时后或者收到rst之后判断连接关闭了，那么清楚nat会话。
3. 还有就是收到FIN包交换之后，也会正常清楚nat会话。

## nat和udp

udp和tcp不同，没有哪些判断连接状态的包。nat在创建udp绑定时，会开启一个映射定时器（mapping timer）。

- 一般映射定时器至少为2分钟，推荐为5分钟。定时器到期会清除nat绑定。
- 当数据报从内传输到外时，nat刷新定时器（对外刷新）；当数据报从外传输到内时，nat刷新定时器（可选 对内刷新）。

## nat和icmp

icmp有两类报文，信息类和出错类。出错类的icmp报文通常包含引起错误条件的ip数据包，所以在通过nat时需要改写里面的ip相关信息，这被称为icmp修复（icmp fix-up）。信息类的报文也存在同样的问题，还包括一个类似tcp和udp端口号的查询ID字段。
