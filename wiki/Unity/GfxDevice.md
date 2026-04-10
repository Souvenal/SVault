# GfxDevice

Unity 底层图形设备架构与资源管理机制。

---

## 渲染设备层次结构

现代 API（D3D12、Vulkan）支持并行指令录制，Unity 实现如下三层架构：

```
GfxThreadableDevice
        ↑
GfxDeviceXXXBase  ←── 每线程一个实例，录制命令
        ↑
GfxDeviceXXX      ←── 全局唯一 DeviceCore，管理资源/帧/Present
```

`XXXBase` 中实现的部分方法都转发给了 Core 的实际实现。

---

## 资源管理

Vulkan 句柄封装采用继承体系：

- 所有 Vulkan 资源继承 `VulkanResource`
- 使用池分配，延迟释放
- **每帧调用垃圾回收**，而不是多个线程分别回收


