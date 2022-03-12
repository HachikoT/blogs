- [资源限制](#资源限制)
  - [内存限制](#内存限制)
  - [CPU限制](#cpu限制)
- [查看容器资源占用](#查看容器资源占用)
- [参考资料](#参考资料)

# 资源限制

docker提供了控制容器资源的功能。docker底层通过linux内核namespace技术来实现资源的隔离，通过linux内核cgroups技术来实现资源的限制。主要包括CPU，内存，磁盘IO资源的限制。

## 内存限制

执行`docker run`命令时能使用的和内存限制相关的选项如下。

| 选项 | 说明 |
| :--: | :--: |
| `-m,--memory` | 内存限制，格式是数字加单位，单位可以为 b,k,m,g。最小为4M |
| `--memory-swap` | 内存+交换分区大小总限制。必须比`-m`设置的大，不设置默认为`-m`的2倍 |
| `--memory-reservation` | 内存软性限制，实际可以超过这个值 |
| `--oom-kill-disable` | 阻止OOM killer杀死超过内存限制的容器 |
| 其他内存选项 | 暂时省略 |

- **内存限制和内存软性限制**

`-m`可以为容器设置内存硬性限制，当容器占用的内存超过这个值那么就会被OOM killer杀死。除非你设置了`--oom-kill-disable`。

`--memory-reservation`可以为容器设置内存软性限制，它不保证任何时刻容器使用的内存不会超过`--memory-reservation`限定的值，它只是确保容器不会长时间占用超过`--memory-reservation`限制的内存大小。在宿主机内存资源紧张时，系统会回收容器的部分内存页，强迫容器的内存占用回到`--memory-reservation`设置的值大小。

## CPU限制

执行`docker run`命令时能使用的和CPU限制相关的选项如下。

| 选项 | 说明 |
| :--: | :--: |
| `--cpuset-cpus` | 允许使用的CPU集，值可以为 0-3,0,1 |
| `-c,--cpu-shares` | CPU共享权值（相对权重） |
| `--cpu-period` | 限制 CPU CFS 的周期，范围从 1ms~1s，即[1000, 1000000] |
| `--cpu-quota` | 限制 CPU CFS 配额，必须不小于1ms，即 >= 1000 |
| `--cpus` | 使用的CPU核数限制 |
| 其他CPU选项 | 暂时省略 |

直接用`--cpus`选项是最简单的，比如设置`--cpus=0.1`那么表示最多用0.1个CPU核。

# 查看容器资源占用

通过`docker stats`命令可以直接查看容器的内存，CPU，IO资源的占用情况。
这里用stress镜像来压测一下，这里开启两个压测进程，会占用200%的CPU（2个CPU核）和512M内存。

```bash
docker run -d --cpus 0.1 -m 1g  lorel/docker-stress-ng stress -vm 2
```

然后可以通过`docker stats`命令看到容器的资源占用情况。可以看到CPU被限制到0.1个核，内存限制1GB所以没有影响。

```bash
CONTAINER ID   NAME               CPU %     MEM USAGE / LIMIT   MEM %     NET I/O       BLOCK I/O    PIDS
326f607c0420   peaceful_poitras   10.00%    516.5MiB / 1GiB     50.44%    3.98kB / 0B   668kB / 0B   5
```


# 参考资料

- [Docker(二十)-Docker容器CPU、memory资源限制](https://www.cnblogs.com/zhuochong/p/9728383.html)
