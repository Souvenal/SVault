# Rendering Pipeline

Vulkan 渲染管线的基本结构：vertex buffer → input assembler → vertex shader → tessellation → geometry shader → rasterization → fragment shader → color blending → framebuffer。

## 管线阶段

```
vertex buffer
    ↓
Input Assembler（固定功能）
    ↓
Vertex Shader（可编程）
    ↓
Tessellation Shader（可选）
    ↓
Geometry Shader（可选）
    ↓
Rasterization（固定功能）
    ↓
Fragment Shader（可编程）
    ↓
Color Blending（固定功能）
    ↓
Framebuffer
```

## 可选阶段

- Tessellation Shader：细分几何体，提升砖墙、楼梯等表面的近景质量
- Geometry Shader：性能较差，仅在 Intel 集显上表现尚可
- Fragment Shader：阴影图生成时不需要

## 固定功能阶段

可改参数但不能改具体过程：Input Assembler、Rasterization、Color Blending。其余阶段可编程。

## 管线不可变原则

Vulkan 渲染管线基本不可变，需预先创建所有管线。优点：便于驱动优化。

## 资源依赖关系

```
Physical Device（选择队列族）
    ↓
Logical Device（依赖队列族、设备扩展、设备特征、功能层）
    ↓
    ├─ Swapchain（依赖物理设备）
    ├─ ImageViews（依赖 Swapchain images）
    ├─ Render Pass（依赖 render targets 和 usage）
    ├─ Framebuffers（依赖 Render Pass 和 ImageViews）
    ├─ Vertex Buffer（依赖 Device，大小需确定）
    ├─ Descriptor Set Layout（依赖各种 binding）
    ├─ Pipeline Layout（依赖 Descriptor Set Layout）
    └─ Texture Image（依赖 Command Buffer）
```

## 命令缓冲区录制顺序

```
begin command buffer
    ↓
begin render pass
    ↓
bind pipeline
    ↓
bind vertex buffers
    ↓
bind descriptor sets（在 index buffer 之前）
    ↓
bind index buffer
    ↓
set viewport
    ↓
set scissor
    ↓
draw
    ↓
end render pass
    ↓
end command buffer
```

## 早期 Z 测试优化

不透明物体使用 `vk::CompareOp::eEqual` 可利用硬件早期 Z 测试。

## Vertex Buffer 优化

驱动开发者推荐将 vertex buffer 和 index buffer 存储在单个 `VkBuffer` 中，通过偏移量区分。这样数据缓存更友好。同一块内存可用于不同时刻使用的不同资源（aliasing），Vulkan 有显式 flags 支持此操作。

## 坐标系统

Vulkan NDC：y 轴向下（与 OpenGL 相反），z 轴与 D3D 相同（0 到 1）。
