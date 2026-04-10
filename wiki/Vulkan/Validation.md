# Validation

Swapchain image 缺少 `VK_IMAGE_USAGE_TRANSFER_DST_BIT` 标志导致的 layout 校验错误。

---

## 常见错误

Swapchain image 没有 `VK_IMAGE_USAGE_TRANSFER_DST_BIT` 标志，无法使用 `vkCmdBlitImage`。

错误信息：

> VUID-VkImageMemoryBarrier-oldLayout-01213: newLayout (VK_IMAGE_LAYOUT_TRANSFER_DST_OPTIMAL) is not compatible with VkImage usage flags VK_IMAGE_USAGE_TRANSFER_SRC_BIT|VK_IMAGE_USAGE_COLOR_ATTACHMENT_BIT

## 解决方案

改用 `vkCmdCopyImage` 或其他不依赖 TRANSFER_DST 的方式。

---

## 相关主题

- [[Vulkan/Extension|Extension]] — Image layout 和 usage 概念
