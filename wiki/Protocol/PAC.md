# PAC

Proxy Auto-Config，代理自动配置脚本。W3C 正式标准，全平台支持。

---

> PAC 是什么？

PAC 是一个 JavaScript 文件，系统/浏览器在每次网络请求时调用 `FindProxyForURL(url, host)` 函数，返回值决定流量走代理还是直连。代理软件用 PAC 实现"规则分流"。

## 函数规范

```javascript
function FindProxyForURL(url, host) {
    // 内网直连
    if (isInNet(host, "192.168.0.0", "255.255.0.0")) {
        return "DIRECT";
    }
    // 走代理
    return "PROXY 127.0.0.1:7890";
}
```

返回值语法：

| 指令 | 含义 |
|------|------|
| `DIRECT` | 直连 |
| `PROXY host:port` | HTTP 代理 |
| `SOCKS5 host:port` | SOCKS5 代理 |

多个指令用分号分隔构成**代理回退链**：

```javascript
return "PROXY 127.0.0.1:7890; SOCKS5 127.0.0.1:7890; DIRECT";
```

先尝试 HTTP 代理 → 失败尝试 SOCKS5 → 失败直连（安全网）。

## 跨平台支持

| 平台 | 设置路径 |
|------|----------|
| Windows | 设置 → 网络 → 代理 → 使用设置脚本 |
| macOS | 系统设置 → 网络 → 高级 → 代理 → 自动代理配置 |
| Linux | 系统设置 → 网络 → 代理 → 自动 |
| iOS | Wi-Fi → 已连接网络 → 配置代理 → 自动 |
| Android | Wi-Fi → 长按已连接网络 → 代理 → 自动 |

## 与 WPAD 的关系

[[WPAD]] = 自动发现 PAC 脚本位置 + 下载执行。WPAD 是 PAC 的自动发现层，最终还是要执行 PAC 脚本。

## 最佳实践

- PAC 脚本托管在私人 Gist，所有设备通过同一 URL 访问
- 不硬编码端口号，使用模板变量（如 Clash 的 `%mixed-port%`）
- 不读系统代理的命令行工具，单独配置环境变量（详见 [[Windows-Use/代理设置]]）

---
