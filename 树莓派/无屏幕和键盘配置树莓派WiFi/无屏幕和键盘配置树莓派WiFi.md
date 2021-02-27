# 无屏幕和键盘配置树莓派WiFi

用户可以在未启动树莓派的状态下单独修改`/boot/wpa_supplicant.conf`文件配置 WiFi 的 SSID 和密码，这样树莓派启动后会自行读取`wpa_supplicant.conf`配置文件连接 WiFi 设备。

```ini
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
ssid="WiFi-A"
psk="12345678"
key_mgmt=WPA-PSK
priority=1
}

network={
ssid="WiFi-B"
psk="12345678"
key_mgmt=WPA-PSK
priority=2
scan_ssid=1
}
```

**ssid**：网络的ssid
**psk**：密码
**key_mgmt**：加密方式
**priority**：连接优先级，数字越大优先级越高（不可以是负数）
**scan_ssid**：连接隐藏WiFi时需要指定该值为1

# 参考资料

- [无屏幕和键盘配置树莓派WiFi和SSH](https://shumeipai.nxez.com/2017/09/13/raspberry-pi-network-configuration-before-boot.html)
- [没有屏幕和键盘也能给树莓派装系统——树莓派安装篇](https://www.imooc.com/article/268242)
