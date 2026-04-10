# systemd 系统管理

Linux 系统和服务管理器，作为 PID 1 运行，负责初始化系统、管理后台服务、处理依赖关系。

## 核心概念：Unit

systemd 管理的基本对象称为 Unit，通过 unit 文件定义（`/etc/systemd/system/` 或 `/lib/systemd/system/`）：

| 类型      | 后缀       | 作用            | 示例                |
| ------- | -------- | ------------- | ----------------- |
| Service | .service | 后台服务/守护进程     | nginx.service     |
| Target  | .target  | 状态/运行级别       | multi-user.target |
| Socket  | .socket  | 套接字激活         | docker.socket     |
| Timer   | .timer   | 定时任务（替代 cron） | apt-daily.timer   |
| Mount   | .mount   | 挂载点管理         | home.mount        |
| Path    | .path    | 路径监控          | backup.path       |

### Target 与 Runlevel 对照

| Target | 旧 Runlevel |
|--------|-------------|
| poweroff.target | 0 |
| rescue.target | 1 |
| multi-user.target | 3 |
| graphical.target | 5 |
| reboot.target | 6 |

## 组件生态

- **systemd (PID 1)**：核心守护进程
- **systemctl**：主要控制命令
- **journald**：结构化日志系统
- **logind**：会话管理
- **networkd**：网络配置（服务器场景）
- **resolved**：DNS 解析缓存
- **timesyncd**：NTP 客户端
- **tmpfiles**：临时文件和目录管理

## 常用命令

```bash
# 服务管理
systemctl start nginx           # 启动
systemctl stop nginx            # 停止
systemctl restart nginx         # 重启
systemctl reload nginx          # 重新加载配置
systemctl enable nginx          # 开机自启
systemctl disable nginx         # 禁用开机自启
systemctl status nginx          # 查看状态
systemctl is-active nginx       # 检查是否运行

# 系统状态
systemctl list-units --type=service --state=running  # 运行中服务
systemctl list-unit-files       # 所有 unit 文件
systemctl get-default           # 查看默认 target
systemctl set-default multi-user.target  # 设置默认 target

# 故障排查
systemctl list-dependencies nginx       # 查看依赖树
systemd-analyze blame           # 启动耗时排名
systemd-analyze critical-chain  # 关键路径

# 日志
journalctl -u nginx             # 查看服务日志
journalctl -u nginx -f          # 实时跟踪
journalctl --since "1 hour ago" # 最近日志
journalctl -p err               # 仅错误级别
journalctl -b                   # 当前启动日志
```

## 自定义 Service 文件

创建 `/etc/systemd/system/myapp.service`：

```ini
[Unit]
Description=My Application Server
After=network.target
Requires=redis.service

[Service]
Type=simple
User=www-data
WorkingDirectory=/var/www/myapp
ExecStart=/usr/bin/python3 app.py
ExecReload=/bin/kill -HUA $MAINPID
Restart=on-failure
RestartSec=5
Environment=APP_ENV=production
EnvironmentFile=/etc/myapp/env

[Install]
WantedBy=multi-user.target
```

启用服务：

```bash
systemctl daemon-reload
systemctl enable myapp
systemctl start myapp
```

## 相关链接

- [[Linux-常用命令]] — 系统信息查询命令
