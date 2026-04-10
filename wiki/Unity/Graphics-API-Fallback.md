# Graphics API Fallback

Unity 在检测到不安全环境时的图形 API 自动回退机制。

---

## Vulkan 验证层导致的回退

如果设备上启用了 Vulkan 校验层，Unity 在**发布版（Release）**会自动回退到 OpenGL ES 2.0。

### 原因分析

```shell
[debug.vulkan.layers]: [VK_LAYER_KHRONOS_validation]
```

- `VK_LAYER_KHRONOS_validation` 是调试层，用于捕获 Vulkan 错误
- Unity 发布版检测到验证层时，认为环境不稳定
- 自动回退到最保守的 OpenGL ES 2.0

### 解决方案

- 调试时启用校验层（Development Build）
- 发布时确保设备没有启用 `VK_LAYER_KHRONOS_validation`


