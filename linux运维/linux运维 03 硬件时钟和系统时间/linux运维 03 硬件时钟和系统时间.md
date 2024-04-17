- [硬件时钟和系统时间](#硬件时钟和系统时间)
  - [时间值](#时间值)
  - [时区](#时区)
- [GMT时间和UTC时间](#gmt时间和utc时间)

# 硬件时钟和系统时间

系统时间由三部分组成：

1. 时间值（local或UTC时间值）。
2. 时区。
3. 是否开启夏令时。

## 时间值

系统时间的时间值的维护分为以下几步：

1. 系统开机的时候，会从硬件时钟（RTC）读取时间值设置为当前的系统时间，然后硬件时钟和系统时间就会独立计时，互不影响。
2. 根据`/etc/adjtime`文件中记录的硬件时钟和系统时钟的偏移值来调整系统时钟的速率，如果设置了ntp服务器，那么会定时从ntp服务器同步正确的系统时间。
3. 关机的时候会自动将当前系统时间保存到硬件时钟，并且更新`/etc/adjtime`中硬件时钟和系统时钟的偏移值。

## 时区

`/etc/localtime`文件记录了系统时间的时区信息，一般是作为软连接指向`/usr/share/zoneinfo`目录下的具体时区文件。

可以执行`timedatectl`命令查看当前的时区，以及其它的一些信息。

```bash
[root@centos ~]# timedatectl
      Local time: Wed 2024-04-17 23:13:31 CST
  Universal time: Wed 2024-04-17 15:13:31 UTC
        RTC time: Wed 2024-04-17 23:13:31
       Time zone: Asia/Shanghai (CST, +0800)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a
```

# GMT时间和UTC时间

GMT（Greenwich Mean Time 格林威治标准时间），是以地球公转的周期来定义秒的概念。

UTC（Universal Time Coordinated 世界协调时间），兼容国际原子时以铯原子震荡周期来定义秒的概念和地球公转周期，为此也引入了闰秒的概念，就是在每年的6月30日或12月31日的最后一分钟选择增加1秒或者减少1秒。
