- [查看cpu信息](#查看cpu信息)
- [查看内存信息](#查看内存信息)
- [查看块设备信息](#查看块设备信息)

# 查看cpu信息

可以使用`lscpu`命令。

```bash
[rc@centos ~]$ lscpu
Architecture:          x86_64                                     # CPU架构
CPU op-mode(s):        32-bit, 64-bit                             # CPU操作模式
Byte Order:            Little Endian                              # 字节序
CPU(s):                2                                          # 逻辑CPU数量
On-line CPU(s) list:   0,1                                        # 在线CPU列表
Thread(s) per core:    1                                          # 每个CPU物理核的超线程数量
Core(s) per socket:    2                                          # 每个插槽CPU物理核数量
Socket(s):             1                                          # 插槽数量
NUMA node(s):          1                                          # NUMA节点数
Vendor ID:             GenuineIntel                               # 制造商
CPU family:            6                                          # CPU家族
Model:                 60                                         # CPU型号
Model name:            Intel(R) Pentium(R) CPU G3260 @ 3.30GHz    # CPU型号名称
Stepping:              3                                          # CPU步进
CPU MHz:               829.431                                    # CPU频率
CPU max MHz:           3300.0000                                  # CPU最大频率
CPU min MHz:           800.0000                                   # CPU最小频率
BogoMIPS:              6585.04                                    # BogoMIPS
Virtualization:        VT-x                                       # 虚拟化
L1d cache:             32K                                        # L1数据缓存
L1i cache:             32K                                        # L1指令缓存
L2 cache:              256K                                       # L2缓存
L3 cache:              3072K                                      # L3缓存
NUMA node0 CPU(s):     0,1                                        # NUMA node0上面的CPU列表
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer xsave rdrand lahf_lm abm epb invpcid_single ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust erms invpcid xsaveopt dtherm arat pln pts md_clear spec_ctrl intel_stibp flush_l1d
```

# 查看内存信息

使用`dmidecode -y memory`查看内存条信息。

```bash
[rc@centos ~]$ sudo dmidecode -t memory
# dmidecode 3.2
Getting SMBIOS data from sysfs.
SMBIOS 2.8 present.

Handle 0x0041, DMI type 16, 23 bytes
Physical Memory Array
        Location: System Board Or Motherboard
        Use: System Memory
        Error Correction Type: None
        Maximum Capacity: 16 GB
        Error Information Handle: Not Provided
        Number Of Devices: 2

Handle 0x0042, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x0041
        Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 4096 MB
        Form Factor: SODIMM
        Set: None
        Locator: ChannelA-DIMM0
        Bank Locator: BANK 0
        Type: DDR3
        Type Detail: Synchronous
        Speed: 1600 MT/s
        Manufacturer: Hynix/Hyundai
        Serial Number: 145B11D1
        Asset Tag: 9876543210
        Part Number: HMT451S6BFR8A-PB
        Rank: 1
        Configured Memory Speed: 1333 MT/s
        Minimum Voltage: 1.35 V
        Maximum Voltage: 1.5 V
        Configured Voltage: 1.5 V

Handle 0x0045, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x0041
        Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 4096 MB
        Form Factor: SODIMM
        Set: None
        Locator: ChannelB-DIMM0
        Bank Locator: BANK 2
        Type: DDR3
        Type Detail: Synchronous
        Speed: 1600 MT/s
        Manufacturer: Hynix/Hyundai
        Serial Number: 0A1498E4
        Asset Tag: 9876543210
        Part Number: HMT451S6BFR8A-PB
        Rank: 1
        Configured Memory Speed: 1333 MT/s
        Minimum Voltage: 1.35 V
        Maximum Voltage: 1.5 V
        Configured Voltage: 1.5 V
```

# 查看块设备信息

使用`lsblk`查看块设备信息。

```bash
[rc@centos ~]$ lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0 119.2G  0 disk
├─sda1            8:1    0     1G  0 part /boot
└─sda2            8:2    0 118.2G  0 part
  ├─centos-root 253:0    0    50G  0 lvm  /
  ├─centos-swap 253:1    0   7.8G  0 lvm  [SWAP]
  └─centos-home 253:2    0  60.5G  0 lvm  /home
```
