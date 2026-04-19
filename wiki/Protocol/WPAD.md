# WPAD

Web Proxy Auto-Discovery Protocol，网络代理自动发现协议。

---

> WPAD 是什么？

WPAD 让客户端自动获取代理配置，无需手动设置。它是 [[PAC]] 的**自动发现层**——WPAD 负责找到 PAC 脚本的 URL，然后执行该脚本。

## 发现流程

按顺序尝试：

1. **DHCP 查询**：向 DHCP 服务器请求 Option 252，返回 PAC 脚本 URL
2. **DNS 查询**：DHCP 失败后，依次查询以下域名的 A 记录：
   - `wpad.<your-domain>`（从 DNS 后缀开始）
   - `wpad.com`
3. 找到后自动下载 `wpad.dat`（本质是 PAC 脚本）并执行

## 适用场景

- 仅企业/校园内网有用：IT 部门统一配置代理策略
- 个人家庭网络没有部署 WPAD 服务器，**永远关闭**（Windows 设置 → 代理 → 自动检测设置 → 关）

## 风险

- **拖慢网络**：Windows 每次连接都执行 DHCP/DNS 查询，超时时间长
- **安全风险**：攻击者可劫持 WPAD 查询，将流量导向恶意代理

---
