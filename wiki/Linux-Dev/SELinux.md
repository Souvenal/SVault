# SELinux

Security-Enhanced Linux，Linux 内核的安全模块，提供**强制访问控制**（MAC）机制，补充传统的**自主访问控制**（DAC）。

## DAC vs MAC

**传统 Linux（DAC）局限：**
- 资源所有者可自主决定访问权限
- root 用户拥有绝对控制权
- 依靠用户/组的权限位（rwx）控制

**SELinux（MAC）增强：**
- 策略由系统管理员定义，资源所有者不能绕过
- 最小权限原则：即使 root 进程也只能访问明确允许的资源
- 细粒度控制：基于进程类型、文件类型、操作类型等多维度标签

## 安全上下文

每个进程（Subject）和文件（Object）都有安全标签，格式为：

```
user:role:type:level
```

- **User**：SELinux 用户（非 Linux 用户），如 `system_u`, `unconfined_u`
- **Role**：定义用户可访问的类型，如 `sysadm_r`, `user_r`
- **Type**：最核心字段，决定访问控制，如 `httpd_t`（Apache）、`etc_t`（配置文件）
- **Level**：多级安全（MLS/MCS），如 `s0:c0.c1023`

## 策略类型

- **Targeted**（默认）：只限制特定守护进程，普通用户进程无限制
- **Strict**：对所有进程实施严格限制
- **MLS**：多级安全（保密级别）

## 模式

- **Enforcing**：强制模式，策略生效，违反操作被拒绝并记录
- **Permissive**：宽容模式，仅记录警告（用于调试）
- **Disabled**：完全关闭

## 工作原理

假设 Apache（`httpd_t`）尝试读取 `/etc/passwd`（`passwd_file_t`）：

1. 内核检查 `httpd_t` 是否允许访问 `passwd_file_t`
2. 查询策略规则
3. 允许则正常读取，拒绝则返回 EACCES，记录 AVC 拒绝日志

即使 Apache 以 root 运行，策略禁止该操作时访问仍被拒绝。

## 常用命令

```bash
# 查看模式
getenforce
sestatus

# 切换模式
setenforce 0  # Permissive
setenforce 1  # Enforcing

# 查看安全上下文
ls -Z /etc/passwd
# 输出：system_u:object_r:passwd_file_t:s0 /etc/passwd
ps -Z | grep httpd

# 修改文件上下文
chcon -t httpd_sys_content_t /var/www/html/index.html
semanage fcontext -a -t httpd_sys_content_t "/var/www/html(/.*)?"
restorecon -Rv /var/www/html

# 审计日志
ausearch -m avc -ts recent
cat /var/log/audit/audit.log | grep denied

# 生成允许规则
ausearch -m avc -ts recent | audit2allow -M mypol
semodule -i mypol.pp
```

## 应用场景

- **企业服务器**：Web、数据库隔离，限制横向移动
- **容器安全**：Docker `--security-opt` 限制容器资源访问
- **Android**：从 4.3 开始强制要求，隔离应用进程
- **政府机构**：满足分级保护、等保 2.0 要求

## 相关链接

- [[动态链接库]] — ldconfig 动态链接库管理
