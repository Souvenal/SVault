# Mac 启动项管理

macOS 启动项管理相关命令。

---

## launchctl

`launchctl` 是 mac 管理 launchd 服务的命令。

### 查看服务状态

```bash
# 查看所有运行的服务
launchctl list 

# 查看特定服务
launchctl list | grep service-name

# 查看详细信息
launchctl print gui/$(id -u)/com.example.service
```

### 管理服务

```bash
# 启用服务
launchctl enable gui/$(id -u)/com.example.service

# 禁用服务（阻止自动启动）
launchctl disable gui/$(id -u)/com.example.service

# 立即启动服务
launchctl kickstart -k gui/$(id -u)/com.example.service

# 发送信号给服务
launchctl kill SIGTERM gui/$(id -u)/com.example.service
```

### 加载/卸载配置文件

```bash
# 加载 plist 文件（已不推荐）
launchctl load /path/to/service.plist 

# 卸载 plist 文件（已不推荐）
launchctl unload /path/to/service.plist

# 推荐使用 bootstrap/bootout
launchctl bootstrap gui/$(id -u) /path/to/service.plist
launchctl bootout gui/$(id -u) /path/to/service.plist
```

---

## 启动项目录

`~/Library` 和 `/Library` 都包含程序的支持文件，分别对应用户级和系统级。

启动项文件在对应的 `/LaunchAgents` 或 `/LaunchDaemons` 下。

---

## sfltool

```bash
sfltool resetbtm
```

可以用 DumpBTM 查询详情。
