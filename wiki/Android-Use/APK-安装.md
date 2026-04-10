# APK 安装

通过 ADB 安装 APK 到 Android 设备。

---

## 安装命令

```bash
adb install foo.apk     # 安装
adb install -r foo.apk  # 覆盖安装（保留数据）
```

## 常用参数

| 参数 | 说明 |
|------|------|
| `-r` | 覆盖现有应用（保留数据） |
| `-d` | 允许版本降级 |
| `-t` | 允许测试包 |
| `-g` | 授予所有运行时权限 |

---

## 连接设备

确保设备启用 USB 调试：
```bash
adb devices          # 查看已连接设备
adb shell            # 进入设备 shell
```
