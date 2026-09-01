[toc]

# 生成dnsConfig

```go
// dns策略 pod.spec.dnsPolicy
// 1. None：空配置
// 2. ClusterFirst+非host网络/ClusterFirstWithHostNet：生成集群dns配置
// 3. ClusterFirst+host网络/Default：使用宿主机dns配置
func getPodDNSType(pod *v1.Pod) (podDNSType, error) {
	dnsPolicy := pod.Spec.DNSPolicy
	switch dnsPolicy {
	case v1.DNSNone:
		return podDNSNone, nil
	case v1.DNSClusterFirstWithHostNet:
		return podDNSCluster, nil
	case v1.DNSClusterFirst:
		if !kubecontainer.IsHostNetworkPod(pod) {
			return podDNSCluster, nil
		}
		// Fallback to DNSDefault for pod on hostnetwork.
		fallthrough
	case v1.DNSDefault:
		return podDNSHost, nil
	}
	return podDNSCluster, fmt.Errorf("invalid DNSPolicy=%v", dnsPolicy)
}
```

```go
func (c *Configurer) GetPodDNS(pod *v1.Pod) (*runtimeapi.DNSConfig, error) {
	// 获取宿主机的dns配置（默认读取/etc/resolv.conf）
	dnsConfig, err := c.getHostDNSConfig(c.ResolverConfig)

	dnsType, err := getPodDNSType(pod)
	switch dnsType {
	case podDNSNone:
		// 1. None：空配置
		dnsConfig = &runtimeapi.DNSConfig{}
	case podDNSCluster:
		if len(c.clusterDNS) != 0 {
			// 2. ClusterFirst+非host网络/ClusterFirstWithHostNet：生成集群dns配置
			dnsConfig.Servers = []string{}
			for _, ip := range c.clusterDNS {
				dnsConfig.Servers = append(dnsConfig.Servers, ip.String())
			}
			dnsConfig.Searches = c.generateSearchesForDNSClusterFirst(dnsConfig.Searches, pod)
			dnsConfig.Options = defaultDNSOptions
			break
		}
		// clusterDNS is not known. Pod with ClusterDNSFirst Policy cannot be created.
		nodeErrorMsg := fmt.Sprintf("kubelet does not have ClusterDNS IP configured and cannot create Pod using %q policy. Falling back to %q policy.", v1.DNSClusterFirst, v1.DNSDefault)
		c.recorder.Eventf(c.nodeRef, v1.EventTypeWarning, "MissingClusterDNS", nodeErrorMsg)
		c.recorder.Eventf(pod, v1.EventTypeWarning, "MissingClusterDNS", "pod: %q. %s", format.Pod(pod), nodeErrorMsg)
		// Fallback to DNSDefault.
		fallthrough
	case podDNSHost:
		// 3. ClusterFirst+host网络/Default：使用宿主机dns配置
		if c.ResolverConfig == "" {
			for _, nodeIP := range c.nodeIPs {
				if utilnet.IsIPv6(nodeIP) {
					dnsConfig.Servers = append(dnsConfig.Servers, "::1")
				} else {
					dnsConfig.Servers = append(dnsConfig.Servers, "127.0.0.1")
				}
			}
			if len(dnsConfig.Servers) == 0 {
				dnsConfig.Servers = append(dnsConfig.Servers, "127.0.0.1")
			}
			dnsConfig.Searches = []string{"."}
		}
	}

	// 追加pod.spec.dnsConfig配置，冲突的优先使用pod.spec.dnsConfig配置
	if pod.Spec.DNSConfig != nil {
		dnsConfig = appendDNSConfig(dnsConfig, pod.Spec.DNSConfig)
	}
	return c.formDNSConfigFitsLimits(dnsConfig, pod), nil
}
```
