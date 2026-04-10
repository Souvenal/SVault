# Pitfalls

Vulkan 开发中的常见陷阱和教训。

---

## AI 生成代码的坑

交给 AI 生成函数名的宏时，AI 可能忘记加 KHR 后缀。

例如：`vkCmdBuildAccelerationStructures` 应为 `vkCmdBuildAccelerationStructuresKHR`。

使用 AI 生成 Vulkan 代码时需额外注意检查 KHR/EXT 扩展名是否完整。
