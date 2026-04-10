# Fixed Function Stages (Vulkan)

Vulkan 固定功能阶段配置。

## Dynamic Viewport 和 Scissor

```cpp
dynamicStates = { VK_DYNAMIC_STATE_VIEWPORT, VK_DYNAMIC_STATE_SCISSOR };
pipelineBuilder.enableDynamicStates(dynamicStates);
// Pipeline 创建时不需要指定 viewport 和 scissor
```

动态方式更灵活，静态方式性能略高。

## Rasterizer

```cpp
rasterizer.depthClampEnable = true;  // clamp 远近平面外的片元而非丢弃，用于 shadow map
rasterizer.rasterizerDiscardEnable = true;  // 几何永远不通过光栅化，禁用帧缓冲
rasterizer.polygonMode = VK_POLYGON_MODE_FILL;  // FILL/LINE/POINT
```

比 1.0f 宽的线宽需要启用 `wideLines` GPU feature。

Shadow bias 可用 `depthBiasEnable` 实现。

## Color Blending

```cpp
if (blendEnable) {
    finalColor.rgb = (srcColorBlendFactor * newColor.rgb) <colorBlendOp>
                     (dstColorBlendFactor * oldColor.rgb);
    finalColor.a = (srcAlphaBlendFactor * newColor.a) <alphaBlendOp>
                   (dstAlphaBlendFactor * oldColor.a);
} else {
    finalColor = newColor;
}

finalColor = finalColor & colorWriteMask;
```

Logic Op：`logicOpEnable = TRUE` 启用按位组合，会自动禁用 `ColorBlendAttachmentState`。

---

## 相关

[[Rendering-Pipeline/Fixed-Function-Stages]] — 通用概念版
