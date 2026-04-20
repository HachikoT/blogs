- [kubeadm init](#kubeadm-init)
  - [certs](#certs)
- [kubeadm join](#kubeadm-join)
- [参考资料](#参考资料)

参考1.33.1 linux环境代码

# kubeadm init

初始化一个k8s控制平面节点。

```text
preflight                    预检
  /IsPrivilegedUser            检查为root用户
  /NumCPU                      检查cpu至少为2核
  /Mem                         检查mem至少为1700MB
  /KubernetesVersion           检查kubeadm的版本不低于要部署的k8s控制平面的版本
  /Firewalld                   检查firewalld服务已关闭
  /Port-xx                     检查:xx端口未被占用（apiserver、controller、scheduler端口）
  /FileAvailable-xx            检查xx文件不存在（apiserver、controller、scheduler、etcd静态pod yaml文件）
  /HTTPProxy                   检查访问apiserver的本地监听地址没有走系统代理
  /HTTPProxyCIDR               检查访问service网段和pod网段没有走系统代理
  /CRI                         检查cri是否在运行（连接cri-socket地址）
  /Swap                        检查swap分区已关闭
  /FileExisting-xx             检查xx可执行文件存在（conntrack、ip、iptables、mount、nsenter命令必须存在，crictl、ebtables、ethtool、socat、tc、touch命令可选）
  /SystemVerification          检查内核版本（至少3.10+），config，cgroup子系统配置是否符合要求
  /Hostname                    检查节点名称是否合法
  /KubeletVersion              检查kubelet的版本最多比kubeadm的版本小3个次要版本，并且不能比k8s控制平面的版本大
  /Service-xx                  检查xx服务存在（kubelet）
  /Port-xx                     检查0.0.0.0:xx端口未被占用（kubelet端口）
  /FileContent-xx              检查xx文件的内容（/proc/sys/net/ipv4/ip_forward为1、/proc/sys/net/ipv6/conf/default/forwarding为1）
  /Port-xx                     检查0.0.0.0:xx端口未被占用（etcd listen端口和peer端口）
  /DirAvailable-xx             检查目录存在，并且为空目录（etcd数据目录）
  /ImagePull                   拉取镜像（apiserver、controller、scheduler、etcd、pause、kube-proxy、coredns），imagePullPolicy可以设置为never跳过下载
                               ${imageRepository}/pause:3.10
                               ${imageRepository}/kube-apiserver:${kubernetesVersion}
                               ${imageRepository}/kube-controller-manager:${kubernetesVersion}
                               ${imageRepository}/kube-scheduler:${kubernetesVersion}
                               ${dns.imageRepository}/coredns:${dns.imageTag} 内部默认v1.11.3
                               ${etcd.local.imageRepository}/etcd:${etcd.local.imageTag} 内部默认3.5.15-0
certs                        生成证书
  /ca                          生成自签名根 CA 用于配置其他 kubernetes 组件
  /apiserver                   生成 apiserver 的证书
  /apiserver-kubelet-client    生成 apiserver 连接到 kubelet 的证书
  /front-proxy-ca              生成前端代理自签名CA(扩展apiserver)
  /front-proxy-client          生成前端代理客户端的证书（扩展 apiserver）
  /etcd-ca                     生成 etcd 自签名 CA
  /etcd-server                 生成 etcd 服务器证书
  /etcd-peer                   生成 etcd 节点相互通信的证书
  /etcd-healthcheck-client     生成 etcd 健康检查的证书
  /apiserver-etcd-client       生成 apiserver 访问 etcd 的证书
  /sa                          生成用于签署服务帐户令牌的私钥和公钥
kubeconfig                   生成建立控制平面和管理所需的所有 kubeconfig 文件
  /admin                       生成一个 kubeconfig 文件供管理员使用以及供 kubeadm 本身使用
  /super-admin                 为超级管理员生成 kubeconfig 文件
  /kubelet                     为 kubelet 生成一个 kubeconfig 文件，*仅*用于集群引导
  /controller-manager          生成 kubeconfig 文件供控制器管理器使用
  /scheduler                   生成 kubeconfig 文件供调度程序使用
etcd                         为本地 etcd 生成静态 Pod 清单文件
  /local                       为本地单节点本地 etcd 实例生成静态 Pod 清单文件
control-plane                生成建立控制平面所需的所有静态 Pod 清单文件
  /apiserver                   生成 kube-apiserver 静态 Pod 清单
  /controller-manager          生成 kube-controller-manager 静态 Pod 清单
  /scheduler                   生成 kube-scheduler 静态 Pod 清单
kubelet-start                写入 kubelet 设置并启动（或重启） kubelet
upload-config                将 kubeadm 和 kubelet 配置上传到 ConfigMap
  /kubeadm                     将 kubeadm 集群配置上传到 ConfigMap
  /kubelet                     将 kubelet 组件配置上传到 ConfigMap
upload-certs                 将证书上传到 kubeadm-certs
mark-control-plane           将节点标记为控制面
bootstrap-token              生成用于将节点加入集群的引导令牌
kubelet-finalize             在 TLS 引导后更新与 kubelet 相关的设置
  /experimental-cert-rotation  启用 kubelet 客户端证书轮换
addon                        安装用于通过一致性测试所需的插件
  /coredns                     将 CoreDNS 插件安装到 Kubernetes 集群
  /kube-proxy                  将 kube-proxy 插件安装到 Kubernetes 集群
show-join-command            显示控制平面和工作节点的加入命令
```

```yaml
---
apiVersion: kubeadm.k8s.io/v1beta4
kind: InitConfiguration
nodeRegistration:
  name: os.Hostname()                                    # 节点名称
  criSocket: "npipe:////./pipe/containerd-containerd"    # 容器运行时socket路径
  imagePullPolicy: "IfNotPresent"                        # 镜像拉取策略，如果设置为never那么kubeadm会跳过下载镜像
  imagePullSerial: true                                  # 串行拉取镜像
localAPIEndpoint:                                        # apiserver监听地址和端口（用于本地访问）
  advertiseAddress: ""
  bindPort: 6443
certificateKey: ""                                       # 用来加密上传到kubeadm-certs secret中的证书密钥的AES密钥
---
apiVersion: kubeadm.k8s.io/v1beta4
kind: ClusterConfiguration
etcd:
  local:
    imageRepository: ""                                  # etcd镜像仓库名
    imageTag: ""                                         # etcd镜像tag名
    dataDir: "/var/lib/etcd"                             # etcd数据目录
    extraArgs:
    extraEnvs:
    serverCertSANs:
    peerCertSANs:
  # external:
  #   endpoints:
  #   caFile:
  #   certFile:
  #   keyFile:
networking:
  serviceSubnet: "10.96.0.0/12"                          # service网段
  podSubnet: ""                                          # pod网段
  dnsDomain: "cluster.local"                             # k8s集群域名
kubernetesVersion: "stable-1"                            # 要部署的k8s控制平面版本
controlPlaneEndpoint: ""                                 # 控制平面的地址（用于集群访问）
apiServer:
  certSANs: []                                           # apiserver证书额外的SANs
dns:
  imageRepository: ""
  imageTag: ""
  disabled: false                                        # 是否禁用coredns插件
proxy:
  disabled: false                                        # 是否禁用kube-proxy插件
ImageRepository: "registry.k8s.io"                       # 镜像仓库名
encryptionAlgorithm: "RSA-2048"                          # 生成证书密钥时使用的加密算法
certificateValidityPeriod: "8760h"                       # 生成证书的有效期（1年）
caCertificateValidityPeriod: "87600h"                    # 生成ca证书的有效期（10年）
```

## certs

生成`ca`证书密钥

```go
// KubeadmCertRootCA is the definition of the Kubernetes Root CA for the API Server and kubelet.
func KubeadmCertRootCA() *KubeadmCert {
	return &KubeadmCert{
		Name:     "ca",
		LongName: "self-signed Kubernetes CA to provision identities for other Kubernetes components",
		BaseName: kubeadmconstants.CACertAndKeyBaseName,  // "ca"
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: "kubernetes",
			},
		},
	}
}

func runCAPhase(ca *certsphase.KubeadmCert) func(c workflow.RunData) error {
	return func(c workflow.RunData) error {
		...
		// 优先使用环境已有的ca证书密钥
		if cert, err := pkiutil.TryLoadCertFromDisk(data.CertificateDir(), ca.BaseName); err == nil {
			...
			if _, err := pkiutil.TryLoadKeyFromDisk(data.CertificateDir(), ca.BaseName); err == nil {
				...
				fmt.Printf("[certs] Using existing %s certificate authority\n", ca.BaseName)
				return nil
			}
			fmt.Printf("[certs] Using existing %s keyless certificate authority\n", ca.BaseName)
			return nil
		}
		...
		// 没有则重新创建
		return certsphase.CreateCACertAndKeyFiles(ca, cfg)
	}
}
```

生成`apiserver`证书密钥

```go
// KubeadmCertAPIServer is the definition of the cert used to serve the Kubernetes API.
func KubeadmCertAPIServer() *KubeadmCert {
	return &KubeadmCert{
		Name:     "apiserver",
		LongName: "certificate for serving the Kubernetes API",
		BaseName: kubeadmconstants.APIServerCertAndKeyBaseName,         // apiserver
		CAName:   "ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: kubeadmconstants.APIServerCertCommonName,       // "kube-apiserver"
				Usages:     []x509.ExtKeyUsage{x509.ExtKeyUsageServerAuth},
			},
		},
		configMutators: []configMutatorsFunc{
			// 添加各种SANs
			makeAltNamesMutator(pkiutil.GetAPIServerAltNames),
		},
	}
}

// GetAPIServerAltNames builds an AltNames object for to be used when generating apiserver certificate
func GetAPIServerAltNames(cfg *kubeadmapi.InitConfiguration) (*certutil.AltNames, error) {
	...
	altNames := &certutil.AltNames{
		DNSNames: []string{
			cfg.NodeRegistration.Name,
			"kubernetes",
			"kubernetes.default",
			"kubernetes.default.svc",
			fmt.Sprintf("kubernetes.default.svc.%s", cfg.Networking.DNSDomain),
		},
		IPs: []net.IP{
			internalAPIServerVirtualIP,  // cfg.LocalAPIEndpoint.AdvertiseAddress
			advertiseAddress,            // cfg.Networking.ServiceSubnet中第一个ip
		},
	}

	// add cluster controlPlaneEndpoint if present (dns or ip)
	if len(cfg.ControlPlaneEndpoint) > 0 {
		if host, _, err := kubeadmutil.ParseHostPort(cfg.ControlPlaneEndpoint); err == nil {
			if ip := netutils.ParseIPSloppy(host); ip != nil {
				altNames.IPs = append(altNames.IPs, ip)
			} else {
				altNames.DNSNames = append(altNames.DNSNames, host)
			}
		} else {
			return nil, errors.Wrapf(err, "error parsing cluster controlPlaneEndpoint %q", cfg.ControlPlaneEndpoint)
		}
	}

	appendSANsToAltNames(altNames, cfg.APIServer.CertSANs, kubeadmconstants.APIServerCertName)

	return altNames, nil
}

func runCertPhase(cert *certsphase.KubeadmCert, caCert *certsphase.KubeadmCert) func(c workflow.RunData) error {
	return func(c workflow.RunData) error {
		...
		// 优先使用环境已有的证书密钥
		if certData, intermediates, err := pkiutil.TryLoadCertChainFromDisk(data.CertificateDir(), cert.BaseName); err == nil {
			...
			caCertData, err := pkiutil.TryLoadCertFromDisk(data.CertificateDir(), caCert.BaseName)
			if err != nil {
				return errors.Wrapf(err, "couldn't load CA certificate %s", caCert.Name)
			}
			...
			if err := pkiutil.VerifyCertChain(certData, intermediates, caCertData); err != nil {
				return errors.Wrapf(err, "[certs] certificate %s not signed by CA certificate %s", cert.BaseName, caCert.BaseName)
			}

			fmt.Printf("[certs] Using existing %s certificate and key on disk\n", cert.BaseName)
			return nil
		}
		...
		// 没有则重新创建
		return certsphase.CreateCertAndKeyFilesWithCA(cert, caCert, cfg)
	}
}
```

生成`apiserver-kubelet-client`证书密钥

```go
// KubeadmCertKubeletClient is the definition of the cert used by the API server to access the kubelet.
func KubeadmCertKubeletClient() *KubeadmCert {
	return &KubeadmCert{
		Name:     "apiserver-kubelet-client",
		LongName: "certificate for the API server to connect to kubelet",
		BaseName: kubeadmconstants.APIServerKubeletClientCertAndKeyBaseName,                  // "apiserver-kubelet-client"
		CAName:   "ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName:   kubeadmconstants.APIServerKubeletClientCertCommonName,              // "kube-apiserver-kubelet-client"
				Organization: []string{kubeadmconstants.ClusterAdminsGroupAndClusterRoleBinding}, // "kubeadm:cluster-admins"
				Usages:       []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth},
			},
		},
	}
}
```

生成`front-proxy-ca`证书密钥

```go
// KubeadmCertFrontProxyCA is the definition of the CA used for the front end proxy.
func KubeadmCertFrontProxyCA() *KubeadmCert {
	return &KubeadmCert{
		Name:     "front-proxy-ca",
		LongName: "self-signed CA to provision identities for front proxy",
		BaseName: kubeadmconstants.FrontProxyCACertAndKeyBaseName,  // "front-proxy-ca"
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: "front-proxy-ca",
			},
		},
	}
}
```

生成`front-proxy-client`证书密钥

```go
// KubeadmCertFrontProxyClient is the definition of the cert used by the API server to access the front proxy.
func KubeadmCertFrontProxyClient() *KubeadmCert {
	return &KubeadmCert{
		Name:     "front-proxy-client",
		BaseName: kubeadmconstants.FrontProxyClientCertAndKeyBaseName,   // "front-proxy-client"
		LongName: "certificate for the front proxy client",
		CAName:   "front-proxy-ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: kubeadmconstants.FrontProxyClientCertCommonName, // "front-proxy-client"
				Usages:     []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth},
			},
		},
	}
}
```

生成`etcd-ca`证书密钥

```go
// KubeadmCertEtcdCA is the definition of the root CA used by the hosted etcd server.
func KubeadmCertEtcdCA() *KubeadmCert {
	return &KubeadmCert{
		Name:     "etcd-ca",
		LongName: "self-signed CA to provision identities for etcd",
		BaseName: kubeadmconstants.EtcdCACertAndKeyBaseName, // "etcd/ca"
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: "etcd-ca",
			},
		},
	}
}
```

生成`etcd-server`证书密钥

```go
// KubeadmCertEtcdServer is the definition of the cert used to serve etcd to clients.
func KubeadmCertEtcdServer() *KubeadmCert {
	return &KubeadmCert{
		Name:     "etcd-server",
		LongName: "certificate for serving etcd",
		BaseName: kubeadmconstants.EtcdServerCertAndKeyBaseName, // "etcd/server"
		CAName:   "etcd-ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				// TODO: etcd 3.2 introduced an undocumented requirement for ClientAuth usage on the
				// server cert: https://github.com/etcd-io/etcd/issues/9785#issuecomment-396715692
				// Once the upstream issue is resolved, this should be returned to only allowing
				// ServerAuth usage.
				Usages: []x509.ExtKeyUsage{x509.ExtKeyUsageServerAuth, x509.ExtKeyUsageClientAuth},
			},
		},
		configMutators: []configMutatorsFunc{
			makeAltNamesMutator(pkiutil.GetEtcdAltNames),
			setCommonNameToNodeName(),
		},
	}
}

// getAltNames builds an AltNames object with the cfg and certName.
func getAltNames(cfg *kubeadmapi.InitConfiguration, certName string) (*certutil.AltNames, error) {
	// advertise address
	advertiseAddress := netutils.ParseIPSloppy(cfg.LocalAPIEndpoint.AdvertiseAddress)
	if advertiseAddress == nil {
		return nil, errors.Errorf("error parsing LocalAPIEndpoint AdvertiseAddress %v: is not a valid textual representation of an IP address",
			cfg.LocalAPIEndpoint.AdvertiseAddress)
	}

	// create AltNames with defaults DNSNames/IPs
	altNames := &certutil.AltNames{
		DNSNames: []string{cfg.NodeRegistration.Name, "localhost"},
		IPs:      []net.IP{advertiseAddress, net.IPv4(127, 0, 0, 1), net.IPv6loopback},
	}

	if cfg.Etcd.Local != nil {
		if certName == kubeadmconstants.EtcdServerCertName {
			appendSANsToAltNames(altNames, cfg.Etcd.Local.ServerCertSANs, kubeadmconstants.EtcdServerCertName)
		} else if certName == kubeadmconstants.EtcdPeerCertName {
			appendSANsToAltNames(altNames, cfg.Etcd.Local.PeerCertSANs, kubeadmconstants.EtcdPeerCertName)
		}
	}
	return altNames, nil
}

func setCommonNameToNodeName() configMutatorsFunc {
	return func(mc *kubeadmapi.InitConfiguration, cc *pkiutil.CertConfig) error {
		cc.CommonName = mc.NodeRegistration.Name
		return nil
	}
}
```

生成`etcd-peer`证书密钥

```go
// KubeadmCertEtcdPeer is the definition of the cert used by etcd peers to access each other.
func KubeadmCertEtcdPeer() *KubeadmCert {
	return &KubeadmCert{
		Name:     "etcd-peer",
		LongName: "certificate for etcd nodes to communicate with each other",
		BaseName: kubeadmconstants.EtcdPeerCertAndKeyBaseName, // "etcd/peer"
		CAName:   "etcd-ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				Usages: []x509.ExtKeyUsage{x509.ExtKeyUsageServerAuth, x509.ExtKeyUsageClientAuth},
			},
		},
		configMutators: []configMutatorsFunc{
			makeAltNamesMutator(pkiutil.GetEtcdPeerAltNames),
			setCommonNameToNodeName(),
		},
	}
}
```

生成`etcd-healthcheck-client`证书密钥

```go
// KubeadmCertEtcdHealthcheck is the definition of the cert used by Kubernetes to check the health of the etcd server.
func KubeadmCertEtcdHealthcheck() *KubeadmCert {
	return &KubeadmCert{
		Name:     "etcd-healthcheck-client",
		LongName: "certificate for liveness probes to healthcheck etcd",
		BaseName: kubeadmconstants.EtcdHealthcheckClientCertAndKeyBaseName,   // "etcd/healthcheck-client"
		CAName:   "etcd-ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: kubeadmconstants.EtcdHealthcheckClientCertCommonName, // "kube-etcd-healthcheck-client"
				Usages:     []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth},
			},
		},
	}
}
```

生成`apiserver-etcd-client`证书密钥

```go
// KubeadmCertEtcdAPIClient is the definition of the cert used by the API server to access etcd.
func KubeadmCertEtcdAPIClient() *KubeadmCert {
	return &KubeadmCert{
		Name:     "apiserver-etcd-client",
		LongName: "certificate the apiserver uses to access etcd",
		BaseName: kubeadmconstants.APIServerEtcdClientCertAndKeyBaseName,   // "apiserver-etcd-client"
		CAName:   "etcd-ca",
		config: pkiutil.CertConfig{
			Config: certutil.Config{
				CommonName: kubeadmconstants.APIServerEtcdClientCertCommonName, // "kube-apiserver-etcd-client"
				Usages:     []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth},
			},
		},
	}
}
```

生成`sa`公私钥

```go
// CreateServiceAccountKeyAndPublicKeyFiles creates new public/private key files for signing service account users.
func CreateServiceAccountKeyAndPublicKeyFiles(certsDir string, keyType kubeadmapi.EncryptionAlgorithmType) error {
	// 优先使用环境已有的公私钥
	_, err := keyutil.PrivateKeyFromFile(filepath.Join(certsDir, kubeadmconstants.ServiceAccountPrivateKeyName))
	if err == nil {
		fmt.Printf("[certs] Using the existing %q key\n", kubeadmconstants.ServiceAccountKeyBaseName)
		return nil
	} else if !os.IsNotExist(err) {
		return errors.Wrapf(err, "file %s existed but it could not be loaded properly", kubeadmconstants.ServiceAccountPrivateKeyName)
	}

	// 不存在则重新创建sa.key和sa.pub
	key, err := pkiutil.NewPrivateKey(keyType)
	if err != nil {
		return err
	}

	// Write .key and .pub files to disk
	fmt.Printf("[certs] Generating %q key and public key\n", kubeadmconstants.ServiceAccountKeyBaseName)

	if err := pkiutil.WriteKey(certsDir, kubeadmconstants.ServiceAccountKeyBaseName, key); err != nil {
		return err
	}

	return pkiutil.WritePublicKey(certsDir, kubeadmconstants.ServiceAccountKeyBaseName, key.Public())
}
```

# kubeadm join

# 参考资料

- [k8s组件版本偏差策略](https://kubernetes.io/zh-cn/releases/version-skew-policy/)

