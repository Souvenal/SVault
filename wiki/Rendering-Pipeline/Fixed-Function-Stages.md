# Fixed Function Stages

固定功能阶段可调整参数但不能改变具体过程。

## Input Assembler

收集顶点数据，组装几何图元。

拓扑类型：`POINT_LIST`、`LINE_LIST`、`TRIANGLE_LIST`、`TRIANGLE_STRIP` 等。

可利用索引复用顶点数据优化。

## Viewport 和 Scissor

Viewport 定义渲染区域，Scissor 定义裁剪区域。

动态 viewport/scissor 更灵活，静态方式性能略高。

## Rasterizer

光栅化阶段将几何图元转换为片元。

关键配置：Polygon Mode（Fill/Line/Point）、Depth Clamp、Depth Bias。

## Color Blending

片元颜色与帧缓冲区现有颜色混合：

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

混合模式：Alpha Blend（透明物体）、Additive（发光效果）、Logic Op（按位操作）。

## Tessellation

通过细分规则增加几何体细节，提升近景质量（如砖墙、楼梯在近处不显平面）。

三个阶段：
- **Hull Shader**：确定细分因子
- **Tessellator**：实际细分
- **Domain Shader**：计算细分后顶点位置

## Geometry Shader

对每个图元进行操作，可用于：
- 图元增加（ Billboard、树叶片元）
- 图元删除（视锥裁剪）
- 图元修改（轮廓检测）

性能开销较大，实际应用中常被省略。

## 相关

[[Vulkan/Fixed-Function-Stages|Vulkan 实现]] — VK 特定配置（Dynamic State、VK_POLYGON_MODE_* 等）
