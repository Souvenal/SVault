# AppData 目录结构

Windows 用户配置文件的**应用数据目录**，类似 Linux 的 `~/.config`、`~/.cache`、`~/.local/share` 集合，三个文件夹分别对应不同的**数据同步策略和权限级别**。

---

## 三种 AppData 目录

### Local（本地固定）

- **作用**：存放**不随用户漫游**的本地数据
- **内容**：缓存、临时文件、大型数据、机器特定配置
- **类比 Linux**：相当于 `~/.cache` + `~/.local/share` 中非同步部分
- **特点**：在域环境中登录其他电脑时，这些数据**不会**跟着走

### Roaming（漫游同步）

- **作用**：存放**随用户配置文件漫游**的数据
- **内容**：应用程序设置、小型配置文件、书签等
- **类比 Linux**：相当于纳入配置管理（如 dotfiles 仓库）的 `~/.config`
- **特点**：在企业域环境中，登录哪台电脑都会同步这些数据

### LocalLow（低完整性）

- **作用**：存放**低完整性级别**进程的数据（主要用于安全沙箱）
- **内容**：浏览器（IE/Edge）沙箱数据、Flash、Java 沙箱等受保护模式的内容
- **类比 Linux**：类似 SELinux 沙箱或容器化应用的隔离存储
- **特点**：权限受限，用于防止恶意代码访问正常用户数据

---

## 目录位置

```
%LOCALAPPDATA%   → C:\Users\<用户名>\AppData\Local
%APPDATA%         → C:\Users\<用户名>\AppData\Roaming
%LOCALAPPDATA%\..\LocalLow → C:\Users\<用户名>\AppData\LocalLow
```

## Linux 对照

| Windows | Linux |
|---------|-------|
| AppData/Local | `~/.cache` + `~/.local/share` |
| AppData/Roaming | `~/.config` |
| AppData/LocalLow | 沙箱应用的隔离存储 |
| Program Files | `/usr/bin` / `/opt` |

**注意**：这三个文件夹存的是"程序的数据"（配置、缓存、用户数据、临时文件），不是程序本体。程序本体在 `Program Files` 或 `Program Files (x86)`。

---

## 相关

- [[Windows-Use/_index|Windows-Use]] — Windows 系统使用笔记索引
