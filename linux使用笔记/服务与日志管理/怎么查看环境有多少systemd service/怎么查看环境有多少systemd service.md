# 怎么查看环境有多少 systemd service

```bash
systemctl list-units --type=service --no-pager
```

参数说明：

- `--type=service`：只查看 service 类型的 unit。
- `--no-pager`：不进入分页器，方便管道处理。
