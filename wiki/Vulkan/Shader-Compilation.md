# Shader Compilation (Vulkan)

Vulkan SPIR-V 相关内容。

## SPIR-V

SPIR-V 是 Vulkan 使用的字节码格式。

## Shader 阶段组合

可将多个 fragment shader 组合到单个 shader module，通过不同 entry point 区分行为。

## Tessellation 和 Geometry Shader

- Tessellation shader：提升表面细节
- Geometry Shader：性能问题，实际少用
- Fragment shader 在阴影图生成时不需要

## 管线配置与 Shader 关系

- Tessellation shader、geometry shader 都是可选阶段
- 可编程阶段可使用 specialization info 优化

## 相关

[[SPIR-V/Compilation]] — 通用编译流程（glslc、libshaderc、Specialization Info）
