# Settings 命令对比

Android Framework 层的 `settings` 命令与 Native 层的 `setprop` 命令的区别。

---

## 核心区别

| 维度 | `settings` | `setprop` |
|------|-----------|-----------|
| **系统层级** | Android 框架层 (Framework) | Linux 内核/Native 层 |
| **数据存储** | SQLite 数据库 | 内存属性系统 |
| **持久性** | ✅ 持久化，重启保留 | ❌ 非持久化（除 `persist.*`） |
| **访问权限** | 需 `WRITE_SETTINGS` 权限 | 严格受限，多数需 root |

## 数据存储位置

```bash
# settings - 保存在数据库
/data/data/com.android.providers.settings/databases/settings.db

# setprop - 保存在内存
/dev/__properties__/
```

## 使用场景

**`settings`** — 用户可见的 UI 设置：
```bash
adb shell settings put system screen_brightness 128
adb shell settings put global adb_enabled 1
```

**`setprop`** — 底层系统调优：
```bash
adb shell setprop debug.hwui.renderer skiavk
adb shell setprop dalvik.vm.heapgrowthlimit 256m
```

## 查看可用键

```bash
# settings
adb shell settings list global
adb shell settings list system
adb shell settings list secure

# setprop
adb shell getprop | grep ro.        # 只读属性
adb shell getprop | grep persist.   # 持久化属性
adb shell getprop | grep debug.     # 调试属性
```

## 快速选择

| 想控制... | 使用 |
|----------|------|
| 屏幕、声音、网络、开发者选项等 UI 设置 | `settings` |
| 调试标志、驱动参数、启动配置 | `setprop` |
| 需重启后仍生效的配置 | `settings` 或 `persist.*` |
| 临时调试 | `setprop` |

---

## 相关链接

- [[Property-Service]] — setprop/getprop 详细说明
