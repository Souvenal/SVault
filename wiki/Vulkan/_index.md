# Vulkan

Vulkan 图形 API：Descriptor 管理系统、光线追踪、扩展机制、ICD 架构。

---

## Pages

- [[Descriptor]] — Descriptor Set 生命周期、Bindless、Uniform vs Non-uniform Indexing
- [[Ray-Tracing]] — 加速结构（BLAS/TLAS）、Shader Binding Table、光线追踪扩展
- [[Extension]] — Instance/Device 扩展区别、Vulkan Core 版本、其他重要扩展
- [[ICD]] — Installable Client Driver 架构：Loader 与 ICD 的关系
- [[Android-Development]] — Android 设备上 Vulkan 校验层开启与调试
- [[Validation]] — Swapchain image layout 校验错误处理
- [[Pitfalls]] — AI 生成代码的常见 KHR 后缀遗漏问题
- [[Render-Pass]] — Attachment、Subpass、Dynamic Rendering
- [[Memory-Management]] — VMA、Aliasing、Host Coherent、Descriptor Pool 溢出
- [[Swapchain]] — Swap Chain 创建、Image Sharing Mode、销毁顺序
- [[Shader-Compilation]] — libshaderc、SPIR-V、Specialization Info
- [[Texture-Sampler]] — Mipmap、Tiling、Comparison Function、MSAA
- [[Synchronization]] — Semaphore、Stage、Fence、Frame Loop 同步
- [[Performance]] — 多线程上传、GPU/CPU 协同优化

---

## 跨 Topic

渲染管线通用概念：[[Rendering-Pipeline/Depth-Testing]]、[[Rendering-Pipeline/Fixed-Function-Stages]]
