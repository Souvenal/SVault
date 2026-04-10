# ICD

Vulkan 采用 ICD（Installable Client Driver）架构，Loader 负责加载和管理多个 ICD，提供统一 API 入口。

---

## 架构

```
Application (Unity)
        ↓
Vulkan Loader
  - 负责加载和管理 ICD
  - 提供统一的 API 入口
  - 在多个 GPU 之间调度
        ↓
ICD (Installable Client Driver)
  - GPU 厂商提供的具体实现
  - NVIDIA: nvoglv64.dll
  - AMD: amdvulkan64.dll
  - Intel: igvk64.dll
        ↓
GPU Hardware
```

Loader 是 Vulkan 运行时与具体 GPU 驱动之间的中间层，应用无需关心底层硬件差异，只需调用统一 API。
