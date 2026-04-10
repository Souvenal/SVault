# Linux 常用命令

Linux 系统日常使用中的常用命令速查。

---

## CPU

查看机器核心数、小端/大端：

```bash
lscpu
```

## Disk

查看目录大小：

```bash
du -sh .
du -sh *
```

## Network

### 网络服务重启

```bash
sudo nmcli networking off
sudo nmcli networking on
```

### 网络连接情况

```bash
lsof -i
# -i: 显示当前网络文件和连接
```

## Environment

### sudo 环境变量

sudo 后会重置到安全的环境变量，使用 `-E` 可继承用户当前变量：

```bash
sudo -E
```

## File System

### chown

修改文件所有者和用户组：

```bash
chown root:root file
chown root: file      # 省略用户组
chown :root file      # 省略用户名，只设用户组
```

## 相关链接

- [[动态链接库]] — ldconfig 动态链接库管理
