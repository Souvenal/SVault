# Property Service

Android 系统属性管理服务。

---

## 概述

Android 拥有一个全局属性系统，由 `init` 进程维护，存储在共享内存中。属性以 **key-value** 形式存在，控制系统各种行为。

## 核心命令

| 命令 | 作用 |
|------|------|
| `getprop` | 获取属性值 |
| `setprop` | 设置属性值 |
| `watchprops` | 监视属性变化 |

## 工作原理

属性存储在 `/dev/__properties__`，通过共享内存访问。多数属性需要 **root 权限** 或特定 SELinux 上下文才能修改。

## 常见用法

```bash
# 设置属性
adb shell setprop debug.sf.hw 1

# 查看属性
adb shell getprop debug.sf.hw

# 查看所有属性
adb shell getprop

# 过滤查看
adb shell getprop | grep debug
```

## 持久化

普通属性重启后丢失。`persist.*` 前缀的属性会写入 `/data/property`，重启保留：

```bash
adb shell setprop persist.myapp.flag 1
```

## 注意事项

- 大多数属性需要 root 权限才能修改
- 某些属性是只读的
- 修改关键属性可能导致系统不稳定

## 与 Settings 对比

详见 [[Settings-命令对比]]。

---

## 相关链接

- [[Settings-命令对比]] — `settings` 与 `setprop` 完整对比
