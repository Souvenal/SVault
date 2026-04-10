# Render Pass

描述 render targets 和使用方式。

## Attachment

Framebuffer 对象引用所有代表 attachment 的 ImageView。

```cpp
// loadOp 和 storeOp 决定渲染前后的数据处理
loadOp = VK_ATTACHMENT_LOAD_OP_LOAD/CLEAR/DONT_CARE;
storeOp = VK_ATTACHMENT_STORE_OP_STORE/DONT_CARE;
```

Subpass 中 attachment 索引直接对应 fragment shader 中的 `layout(location = 0)out vec4 outColor`。

## Subpass

每个 subpass 依赖前一个 subpass 的输出。一个 render pass 可包含多个 subpass 用于延迟渲染等高级技术。

## Dynamic Rendering

无需创建 Render Pass 和 Framebuffer 对象，简化渲染流程。可动态更改附件而不创建新对象。

```cpp
vkCmdBeginRenderingKHR(commandBuffer, &renderingInfo);
// 渲染
vkCmdEndRenderingKHR(commandBuffer);
```

## Framebuffer

一个 Framebuffer 对象引用所有 attachment 的 ImageView。

创建 Framebuffer 前需要先创建对应的 ImageView。

## 渲染顺序

高层渲染流程：

1. Wait for the previous frame to finish
2. Acquire an image from the swap chain
3. Record a command buffer which draws the scene onto that image
4. Submit the recorded command buffer
5. Present the swap chain image
