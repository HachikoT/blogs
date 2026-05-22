- [系统信息（System Info）](#系统信息system-info)
- [cpu和内存信息](#cpu和内存信息)
- [参考资料](#参考资料)

# 系统信息（System Info）

通过cadvisor获取Machine ID、System UUID、Boot ID、Kernel Version、OS Image的值

- **Machine ID**：是一个持久固定的机器标识，如果文件不存在且系统使用 systemd，启动时会自动生成（systemd-machine-id-setup）
- **System UUID**：（或称 SMBIOS UUID / DMI UUID）是写入固件（BIOS/UEFI）的硬件标识符，由主板制造商在生产时烧录，通常不可更改
- **Boot ID**：是一个临时的机器表示，每次系统启动时，内核在初始化随机数子系统后生成一个新的随机 UUID

```go
// kubernetes/pkg/kubelet/nodestatus/setters.go

func MachineInfo(
	...
	machineInfoFunc func() (*cadvisorapiv1.MachineInfo, error), // Kubelet.GetCachedMachineInfo
	...
) Setter {
	return func(ctx context.Context, node *v1.Node) error {
		...
		// 从cadvisor获取machineInfo，从里面获取Machine ID、System UUID、Boot ID
		info, err := machineInfoFunc()
		...
			node.Status.NodeInfo.MachineID = info.MachineID
			node.Status.NodeInfo.SystemUUID = info.SystemUUID
			...
			node.Status.NodeInfo.BootID = info.BootID
		...
	}
}
```

```go
// kubernetes/vendor/github.com/google/cadvisor/machine/info.go

func Info(sysFs sysfs.SysFs, fsInfo fs.FsInfo, inHostNamespace bool) (*info.MachineInfo, error) {
	...

	// 读取 /sys/class/dmi/id/product_uuid
	systemUUID, err := sysinfo.GetSystemUUID(sysFs)
	...
	machineInfo := &info.MachineInfo{
		...
		MachineID:        getInfoFromFiles(filepath.Join(rootFs, *machineIDFilePath)),  // 读取 /etc/machine-id
		SystemUUID:       systemUUID,
		BootID:           getInfoFromFiles(filepath.Join(rootFs, *bootIDFilePath)),     // 读取 /proc/sys/kernel/random/boot_id
		...
	}
	...
	return machineInfo, nil
}
```

- **Kernel Version**：内核版本
- **OS Image**：发行版本
- **Container Runtime Version**：容器运行时版本

```go
// kubernetes/pkg/kubelet/nodestatus/setters.go

func VersionInfo(
	versionInfoFunc func() (*cadvisorapiv1.VersionInfo, error),                  // typically Kubelet.cadvisor.VersionInfo
	runtimeTypeFunc func() string,                                               // typically Kubelet.containerRuntime.Type
	runtimeVersionFunc func(ctx context.Context) (kubecontainer.Version, error), // typically Kubelet.containerRuntime.Version
) Setter {
	return func(ctx context.Context, node *v1.Node) error {
		verinfo, err := versionInfoFunc()
		...
		// 从cadvisor获取系统内核版本和发行版本
		node.Status.NodeInfo.KernelVersion = verinfo.KernelVersion
		node.Status.NodeInfo.OSImage = verinfo.ContainerOsVersion

		// 从containerRuntime接口获取运行时版本
		runtimeVersion := "Unknown"
		if runtimeVer, err := runtimeVersionFunc(ctx); err == nil {
			runtimeVersion = runtimeVer.String()
		}
		node.Status.NodeInfo.ContainerRuntimeVersion = fmt.Sprintf("%s://%s", runtimeTypeFunc(), runtimeVersion)
		...
		return nil
	}
}
```

```go
// kubernetes/vendor/github.com/google/cadvisor/machine/info.go

func ContainerOsVersion() string {
	// 读取 /etc/os-release中PRETTY_NAME字段的值
	os, err := getOperatingSystem()
	if err != nil {
		os = "Unknown"
	}
	return os
}

func KernelVersion() string {
	// 获取uname -r的内容
	uname := &unix.Utsname{}

	if err := unix.Uname(uname); err != nil {
		return "Unknown"
	}

	return string(uname.Release[:bytes.IndexByte(uname.Release[:], 0)])
}
```

# cpu和内存信息

通过cadvisor获取Capacity的cpu、memory、hugePages的值，并通过减去SystemReserved，KubeReserved，evictionReservation的值获取Allocatable的值

```go
// kubernetes/pkg/kubelet/nodestatus/setters.go

func CapacityFromMachineInfo(info *cadvisorapi.MachineInfo) v1.ResourceList {
	c := v1.ResourceList{
		v1.ResourceCPU: *resource.NewMilliQuantity(
			int64(info.NumCores*1000),
			resource.DecimalSI),
		v1.ResourceMemory: *resource.NewQuantity(
			int64(info.MemoryCapacity),
			resource.BinarySI),
	}

	for _, hugepagesInfo := range info.HugePages {
		pageSizeBytes := int64(hugepagesInfo.PageSize * 1024)
		hugePagesBytes := pageSizeBytes * int64(hugepagesInfo.NumPages)
		pageSizeQuantity := resource.NewQuantity(pageSizeBytes, resource.BinarySI)
		c[v1helper.HugePageResourceName(*pageSizeQuantity)] = *resource.NewQuantity(hugePagesBytes, resource.BinarySI)
	}

	return c
}

func MachineInfo(
	...
	machineInfoFunc func() (*cadvisorapiv1.MachineInfo, error), // typically Kubelet.GetCachedMachineInfo
	...
	nodeAllocatableReservationFunc func() v1.ResourceList,      // typically Kubelet.containerManager.GetNodeAllocatableReservation
	...
) Setter {
	return func(ctx context.Context, node *v1.Node) error {
		...
		info, err := machineInfoFunc()
		...
			// 从cadvisor获取Capacity.cpu，Capacity.memory，Capacity.hugepages-${PAGE_SIZE}
			for rName, rCap := range cadvisor.CapacityFromMachineInfo(info) {
				node.Status.Capacity[rName] = rCap
			}
		...
		// Allocatable.cpu和Allocatable.hugePages是通过Capacity减去SystemReserved，KubeReserved的值获取的
		// Allocatable.memory是通过Capacity减去SystemReserved，KubeReserved，evictionReservation的值获取的，其中evictionReservation的默认值是100Mi
		allocatableReservation := nodeAllocatableReservationFunc()
		for k, v := range node.Status.Capacity {
			value := v.DeepCopy()
			if res, exists := allocatableReservation[k]; exists {
				value.Sub(res)
			}
			...
			node.Status.Allocatable[k] = value
		}
		...
		// 对于memory来说还要减去hugePages占用的内存
		for k, v := range node.Status.Capacity {
			if v1helper.IsHugePageResourceName(k) {
				allocatableMemory := node.Status.Allocatable[v1.ResourceMemory]
				value := v.DeepCopy()
				allocatableMemory.Sub(value)
				...
				node.Status.Allocatable[v1.ResourceMemory] = allocatableMemory
			}
		}
		return nil
	}
}
```

```go
// kubernetes/vendor/github.com/google/cadvisor/machine/info.go

func Info(sysFs sysfs.SysFs, fsInfo fs.FsInfo, inHostNamespace bool) (*info.MachineInfo, error) {
	...
	// 读取/proc/meminfo中的MemTotal字段
	memoryCapacity, err := GetMachineMemoryCapacity()
	...
	// 读取/sys/kernel/mm/hugepages/，获取系统各种pageSize的hugePage的大小
	hugePagesInfo, err := sysinfo.GetHugePagesInfo(sysFs, hugepagesDirectory)
	...
	// 读取/sys/devices/system/node/，获取cpu拓扑信息，numCores为系统所有逻辑核数量
	topology, numCores, err := GetTopology(sysFs)
	...
	machineInfo := &info.MachineInfo{
		...
		NumCores:         numCores,
		...
		MemoryCapacity:   memoryCapacity,
		...
		HugePages:        hugePagesInfo,
		...
	}
	...
	return machineInfo, nil
}
```

# 参考资料

- [为系统守护进程预留计算资源](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/reserve-compute-resources/)
