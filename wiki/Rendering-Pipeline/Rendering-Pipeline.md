# Rendering Pipeline

渲染管线通用概念与阶段总览。

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

- Tessellation Shader：细分几何体，提升近景质量
- Geometry Shader：性能较差，实际应用中常被省略
- Fragment Shader：阴影图生成时不需要

## 固定功能阶段

可改参数但不能改具体过程：Input Assembler、Rasterization、Color Blending。其余阶段可编程。

## 管线不可变原则

大多数图形 API（Vulkan、Metal、Direct3D）渲染管线基本不可变，需预先创建所有管线。优点：便于驱动优化。

## 坐标系统

不同 API 的 NDC 坐标系统不同：
- **Vulkan**：y 轴向下，z 轴 0 到 1
- **OpenGL**：y 轴向上，z 轴 -1 到 1
- **Direct3D**：y 轴向上，z 轴 0 到 1

## 相关

[[Vulkan/Rendering-Pipeline|Vulkan 实现]] — 资源依赖链、命令录制顺序
