# Synchronization

Vulkan 同步原语：Semaphore、Stage、Fence。

## Submit 机制

Submit 时指定：

- `wait` 的 semaphore、stage
- `signal` 的 semaphore、stage

语义：等待前者的 semaphore 完成后才能开始 wait 的 stage，完成后者 stage 后激活对应 semaphore。

```cpp
VkSemaphore waitSemaphores[] = {imageAvailableSemaphore};
VkPipelineStageFlags waitStages[] = {VK_PIPELINE_STAGE_COLOR_ATTACHMENT_OUTPUT_BIT};

VkSemaphore signalSemaphores[] = {renderFinishedSemaphore};
VkPipelineStageFlags signalStages[] = {VK_PIPELINE_STAGE_COLOR_ATTACHMENT_OUTPUT_BIT};

submitInfo.waitSemaphoreCount = 1;
submitInfo.pWaitSemaphores = waitSemaphores;
submitInfo.pWaitDstStageMask = waitStages;
submitInfo.signalSemaphoreCount = 1;
submitInfo.pSignalSemaphores = signalSemaphores;
```

## Fence

Fence 用于 CPU 侧等待 GPU 完成操作。

```cpp
vkWaitForFences(device, 1, &fence, VK_TRUE, UINT64_MAX);
vkResetFences(device, 1, &fence);
```

也可使用 `vkQueueWaitIdle` 等待队列空闲。

## 多传输操作

Fence 允许同时调度多个传输并等待全部完成，比逐个等待有更多优化空间。

## 隐式同步

某些操作有隐式 `VK_ACCESS_HOST_WRITE_BIT` 同步。

## 传输队列约束

`vkCmdBlitImage` 必须提交到有图形能力的队列，即使使用 dedicated transfer queue。

## 队列族能力

支持图形或计算的队列族一定支持传输。

## Frame Loop 同步

典型帧循环：

1. Wait for previous frame
2. Acquire image from swap chain
3. Record command buffer
4. Submit with sync
5. Present

每个阶段都需要正确的同步原语确保数据流正确。
