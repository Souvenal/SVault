# Performance

Vulkan 性能优化相关内容。

## 多线程上传

Mesh、Texture 等数据的上传场景。

### 低效模式：CPU 阻塞等待

```cpp
// 录制 upload 命令
vkBeginCommandBuffer(uploadCmd, &beginInfo);
vkCmdCopyBuffer(uploadCmd, stagingBuffer, vertexBuffer, 1, &copyRegion);
vkEndCommandBuffer(uploadCmd);

// 提交后等待 GPU 执行完毕 → CPU 阻塞
VkSubmitInfo submitInfo = {.commandBufferCount = 1, .pCommandBuffers = &uploadCmd};
vkQueueSubmit(transferQueue, 1, &submitInfo, VK_NULL_HANDLE);
vkQueueWaitIdle(transferQueue);  // CPU 在此阻塞，等待 GPU 完成

// GPU 执行完毕后，CPU 才能继续处理其他逻辑
processNextTask();
```

问题：CPU 等待 GPU 期间完全空闲，无法处理其他任务。Mesh 越大，阻塞时间越长。

### 推荐模式：后台线程专职上传

```cpp
// 后台线程循环
void uploadThread() {
    while (running) {
        // 取出上传任务
        UploadTask task = uploadQueue.pop();

        // 录制并提交命令
        vkBeginCommandBuffer(uploadCmd, &beginInfo);
        vkCmdCopyBuffer(uploadCmd, task.staging, task.dstBuffer, 1, &task.region);
        vkEndCommandBuffer(uploadCmd);
        vkQueueSubmit(transferQueue, 1, &submitInfo, task.fence);

        // 等待完成后复用或删除 staging buffer
        vkWaitForFences(device, 1, &task.fence, VK_TRUE, UINT64_MAX);
        recycleStagingBuffer(task.staging);
    }
}
```

特点：
- 主线程提交任务后立即返回，不阻塞
- 后台线程专职处理所有 GPU 数据上传
- Staging buffer 可复用，减少分配/释放开销
- 多个上传可并行调度（多个 fence）

## 相关

[[Synchronization]] — 同步原语（Semaphore、Stage、Fence）
