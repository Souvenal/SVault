# Texture and Sampler

纹理在 Vulkan 中由 `VkImage` 表示，像素布局可变化。

## Tiling Mode

Tiling Mode 控制纹理的内存布局方式。创建后不能修改。

| Tiling Mode | 内存布局 | 访问特点 |
|---|---|---|
| `VK_IMAGE_TILING_LINEAR` | 行主序，texel 按 x,y,z 顺序排列 | CPU 可直接访问，适合 map/unmap |
| `VK_IMAGE_TILING_OPTIMAL` | 实现相关，硬件优化布局 | GPU 高效访问，CPU 访问效率低 |

### Linear Tiling

Texel 在内存中按行排列，`rowPitch = width * bytesPerPixel`。CPU 可通过 `vkMapMemory` 直接读写：

```cpp
vkMapMemory(device, memory, offset, size, 0, &ptr);
for (int y = 0; y < height; y++) {
    for (int x = 0; x < width; x++) {
        ptr[y * rowPitch + x * bytesPerPixel] = ...;
    }
}
```

缺点：GPU 采样效率低，带宽和缓存利用不优化。

### Optimal Tiling

硬件自行决定最佳布局（可能使用 tiles、swizzles 等技术）。GPU 采样高效，但 CPU 访问困难。

**大多数场景用 Optimal**。只有以下情况用 Linear：
- Staging texture（中间态，用于上传数据）
- CPU 需要直接访问纹理数据（如截图、调试）

### 限制

- Swapchain image 必须是 Linear Tiling（才能被显示）
- 某些格式可能只支持一种 Tiling Mode

## Mipmap

运行时生成 mipmap 不常见，通常预生成存储在纹理文件中。

LOD 计算：

```cpp
lod = getLodLevelFromScreenSize();  // 物体近时更小，可能为负
lod = clamp(lod + mipLodBias, minLod, maxLod);
level = clamp(floor(lod), 0, texture.mipLevels - 1);

if (mipmapMode == VK_SAMPLER_MIPMAP_MODE_NEAREST) {
    color = sample(level);
} else {
    color = blend(sample(level), sample(level + 1));
}
```

采样操作受 LOD 影响：

```cpp
if (lod <= 0) {
    color = readTexture(uv, magFilter);
} else {
    color = readTexture(uv, minFilter);
}
```

## Comparison Function

```cpp
samplerInfo.compareEnable = VK_TRUE;
samplerInfo.compareOp = VK_COMPARE_OP_LESS;  // 用于 PCF shadow map
```

启用后，texel 先与某值比较，比较结果用于过滤操作。主要用于 Percentage-Closer Filtering (PCF) 阴影图。

## VK_FILTER_LINEAR

启用插值过滤。

## MSAA (Multi-Sample Anti-Aliasing)

超采样 Image 不能直接显示，需要解析（resolve）成普通 Image。

```cpp
VkImageResolve resolveRegion;
resolveRegion.srcOffset = {0, 0, 0};
resolveRegion.dstOffset = {0, 0, 0};
resolveRegion.extent = imageExtent;
resolveRegion.srcSubresource.aspectMask = VK_IMAGE_ASPECT_COLOR_BIT;
resolveRegion.dstSubresource.aspectMask = VK_IMAGE_ASPECT_COLOR_BIT;

vkCmdResolveImage(commandBuffer, srcImage, srcLayout,
                   dstImage, dstLayout, 1, &resolveRegion);
```

## Sampler 和 Image 分离

推荐将 Sampler 和 Image（View）分开管理，增强灵活性。

## 纹理数据上传

通过 staging buffer 上传纹理数据到 GPU，使用 `vkCmdCopyBufferToImage`。
