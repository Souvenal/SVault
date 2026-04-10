# Swapchain

负责图像的呈现与获取。

## 创建依赖

Swapchain 在逻辑设备创建后、渲染前创建，但创建信息来自物理设备。

```cpp
// 创建时需要物理设备提供的格式支持信息
VkSurfaceFormatKHR surfaceFormat = chooseSurfaceFormat(physicalDevice);
VkPresentModeKHR presentMode = choosePresentMode(physicalDevice);
VkExtent2D extent = chooseSwapExtent(physicalDevice);
```

## Image Sharing Mode

- `VK_SHARING_MODE_EXCLUSIVE`：单队列使用，性能更好
- `VK_SHARING_MODE_CONCURRENT`：多队列访问，需指定队列族

单设备情况下，图形队列和呈现队列通常是同一队列族，使用 EXCLUSIVE mode。

## Image Views

Swapchain 创建的 ImageView 需要手动销毁（Image 由 swapchain 自动管理）。

```cpp
for (auto& imageView : swapChainImageViews) {
    vkDestroyImageView(device, imageView, nullptr);
}
```

## 与窗口尺寸不匹配

Swapchain 和 window 的宽高可能不同。

## 重建条件

- 窗口大小改变
- 从 sRGB 切换到 HDR（可能需要重建 Render Pass）
- 其他格式/模式变化

重建时指定旧 swapchain，新 swapchain 创建信息来自物理设备。

## 销毁顺序

Swapchain 先于逻辑设备销毁。

## Stereographics 3D

多图层应用（如 3D 立体）需要设置 `subResourceRange.layerCount = 2`。

## Component 通道颜色映射

创建 ImageView 时可指定 `componentMapping` 进行通道映射。

## 裁剪模式

交换链可设置为裁剪模式，渲染区域外的内容被丢弃。

## 校验层问题

Vulkan 早期实现区分实例和设备特定的验证层，新版本已忽略 `VkDeviceCreateInfo` 的 `enabledLayerCount` 和 `ppEnabledLayerNames`。
