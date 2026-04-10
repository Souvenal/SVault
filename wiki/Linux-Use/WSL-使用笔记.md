# WSL 使用笔记

Windows Subsystem for Linux 相关配置和技巧。

## systemd

在 `/etc/wsl.conf` 中配置：

```ini
[boot]
systemd=true
```

使用 `systemctl list-unit-files --type=service` 检查 systemd 运行状态。

## 相关链接

- [[systemd-系统管理]] — systemd 详细说明
