# Extension

Vulkan 扩展分 Instance 级和 Device 级，核心版本（Vulkan 1.1/1.2）将常用扩展纳入核心。Instance 扩展提供全局基础设施，Device 扩展暴露硬件能力。

---

## Instance Extension vs Device Extension

| 特性 | Instance Extension | Device Extension |
|---|---|---|
| 作用范围 | Vulkan 实例级别 | 物理设备/逻辑设备级别 |
| 查询方式 | `vkEnumerateInstanceExtensionProperties` | `vkEnumerateDeviceExtensionProperties` |
| 启用时机 | `vkCreateInstance` 时 | `vkCreateDevice` 时 |
| 影响对象 | 全局 Vulkan 运行时 | 特定 GPU 设备 |

### Device Extension（设备扩展）

影响特定物理设备，通常与 GPU 硬件功能相关：

```cpp
// 渲染核心
VK_KHR_swapchain                    // 交换链（显示输出）
VK_KHR_multiview                    // 单通道立体渲染
VK_KHR_depth_stencil_resolve        // MSAA 深度解析
VK_KHR_fragment_shading_rate         // VRS 可变速率着色
VK_KHR_dynamic_rendering            // 无需 Render Pass 的动态渲染
VK_KHR_synchronization2              // 同步机制升级

// 内存/资源
VK_KHR_dedicated_allocation          // 专用内存分配
VK_KHR_external_memory               // 外部内存共享
VK_KHR_sampler_ycbcr_conversion      // YCbCr 采样器

// Shader 功能
VK_KHR_shader_float16_int8           // float16/int8 支持
VK_KHR_shader_atomic_int64           // int64 原子操作

// 平台特定
VK_ANDROID_external_memory_android_hardware_buffer  // Android AHardwareBuffer
```

启用方式：

```cpp
VkDeviceCreateInfo createInfo = {};
createInfo.enabledExtensionCount = deviceExtCount;
createInfo.ppEnabledExtensionNames = deviceExts;
vkCreateDevice(physicalDevice, &createInfo, nullptr, &device);
```

### Instance Extension（实例扩展）

影响整个 Vulkan 实例，通常与平台抽象层、全局功能相关：

```cpp
// 平台 Surface 创建
VK_KHR_surface                      // 基础 surface 抽象
VK_KHR_android_surface              // Android 平台
VK_KHR_win32_surface                // Windows 平台
VK_KHR_wayland_surface              // Linux Wayland

// 调试/验证
VK_EXT_debug_report                 // 调试回调（旧）
VK_EXT_debug_utils                  // 调试回调（新）

// 全局能力查询
VK_KHR_get_physical_device_properties2   // 查询设备属性2
VK_KHR_external_memory_capabilities      // 外部内存能力查询
```

启用方式：

```cpp
VkInstanceCreateInfo createInfo = {};
createInfo.enabledExtensionCount = instanceExtCount;
createInfo.ppEnabledExtensionNames = instanceExts;
vkCreateInstance(&createInfo, nullptr, &instance);
```

### 代码中的典型用法

```cpp
// Instance Extension - 在创建 Instance 时检查
if (vk::HasExtension(vk::InstanceExtension::kVK_KHR_get_physical_device_properties2)) {
    // 可使用 vkGetPhysicalDeviceProperties2 查询更多设备属性
}

// Device Extension - 在创建 Device 时检查
if (vk::HasExtension(vk::DeviceExtension::kVK_KHR_swapchain)) {
    // 可创建 Swapchain 进行显示
}
```

### 为什么分开？

1. **多 GPU 场景**：一个 Instance 可管理多个 Device，每个 Device 支持的扩展可能不同
2. **驱动加载时机**：Instance 扩展在加载 Vulkan loader 时就需要，Device 扩展可延迟到选择 GPU 后
3. **功能分层**：Instance 扩展是"全局基础设施"，Device 扩展是"硬件能力"

---

## Vulkan Core 版本演进

### Vulkan 1.1

- `GetPhysicalDeviceFeatures2`：1.0 中需要查询并启用 KHR 扩展的功能，1.1 纳入核心

### Vulkan 1.2

- **ScalarBlockLayout**：标量布局
- **spirv-1.4**：SPIR-V 着色器版本更新

### Vulkan 1.3

- **VK_KHR_dynamic_rendering**：无需 Render Pass 的动态渲染
- **VK_KHR_synchronization2**：同步机制升级，依赖处理更高效

---

## 其他重要扩展

### Deferred Host Operations

`VK_KHR_deferred_host_operations` 本身不提供功能，而是作为其他扩展的依赖。若扩展声明依赖此扩展，则可支持将操作延迟到后台线程执行（多核加速）。

**依赖此扩展的 deferral extensions**：
- `VK_KHR_acceleration_structure`

### Ray Tracing

有 NV 版本和 KHR 版本，NV 是早期版本现在仅用于兼容性，正常只需查询 KHR：

- `VK_KHR_acceleration_structure` — 加速结构
- `VK_KHR_ray_tracing_pipeline` — 光线追踪管线
- `VK_KHR_ray_query` — 光线查询

### Misc

- **ShaderClock**：可在 shader 中调用时钟计时

---

## 相关主题

- [[Vulkan/Ray-Tracing|Ray-Tracing]] — 光线追踪扩展详解
- [[Vulkan/Descriptor|Descriptor]] — VK_EXT_descriptor_indexing
