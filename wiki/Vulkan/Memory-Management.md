# Memory Management

Vulkan 将内存管理责任交给应用，驱动只负责分配。

## VulkanMemoryAllocator (VMA)

GPUOpen 提供的 VMA 库简化内存管理。不应该为所有 buffer 用 `memoryAllocate`，应该用自定义 allocator，通过 offset 控制。

## Aliasing

同一块内存可用于不同时刻使用的不同资源，只要在使用前刷新数据即可。`VkImage` 和 `VkBuffer` 都支持 aliasing，Vulkan 提供显式 flags 标识 aliasing 意图。

## Host Coherent Memory

使用 `VK_MEMORY_PROPERTY_HOST_COHERENT_BIT` 的 heap：

- 写入后调用 `vkFlushMappedMemoryRanges`
- 读取前调用 `vkInvalidateMappedMemoryRanges`

## 资源依赖链

```
Logical Device
    ├─ Memory（VkDeviceMemory）
    │   └─ Buffer（VkBuffer）
    │       └─ 用 vkBindBufferMemory 绑定
    └─ Image（VkImage）
        └─ 用 vkBindImageMemory 绑定
```

## 多资源单 allocation

驱动推荐从单一 allocation 分配多个资源（如 vertex buffer + index buffer），数据缓存更友好。
