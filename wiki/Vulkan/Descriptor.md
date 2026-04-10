# Descriptor

Descriptor Set 是 GPU 资源的抽象接口，贯穿 Layout → Allocate → Write → Bind 四个阶段。Bindless 扩展支持运行时动态索引数组，区分 Uniform 与 Non-uniform Indexing 两种访问模式。

---

## 生命周期四阶段

### Layout 阶段（接口定义）

`VkDescriptorSetLayoutBinding` 仅声明接口类型，不分配实际资源：

```cpp
VkDescriptorSetLayoutBinding bindings[] = {
    { .binding = 0, .descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER, .stageFlags = VK_SHADER_STAGE_VERTEX_BIT },
    { .binding = 1, .descriptorType = VK_DESCRIPTOR_TYPE_COMBINED_IMAGE_SAMPLER, .stageFlags = VK_SHADER_STAGE_FRAGMENT_BIT }
};
vkCreateDescriptorSetLayout(..., &bindings, ..., &descriptor_set_layout);
vkCreatePipelineLayout(&descriptor_set_layout, ..., &pipeline_layout);
```

### Allocate 阶段（实例化）

从 Pool 分配空容器，形状由 Layout 决定：

```cpp
vkAllocateDescriptorSets(&descriptor_pool, &allocate_info, &descriptor_set);
// descriptor_set = [binding 0: ❌空, binding 1: ❌空]
```

### Write 阶段（填入资源）

将实际 Buffer/Image 句柄写入 Descriptor Set：

```cpp
VkDescriptorBufferInfo buffer_info = { .buffer = buffer, .offset = 0, .range = VK_WHOLE_SIZE };
VkWriteDescriptorSet write = {
    .dstSet = descriptor_set,
    .dstBinding = 0,
    .descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER,
    .pBufferInfo = &buffer_info
};
vkUpdateDescriptorSets(device, 1, &write, 0, nullptr);
// descriptor_set = [binding 0: ✅ buffer_handle, binding 1: ❌空]
```

### Bind 阶段（绑定使用）

绑定到命令缓冲区，后续 Draw/Dispatch 可访问：

```cpp
vkCmdBindDescriptorSets(cmd_buffer, pipeline_layout, 0, 1, &descriptor_set, 0, nullptr);
```

---

## Bindless（Descriptor Indexing）

核心扩展：`VK_EXT_descriptor_indexing`

### 关键 Binding Flags

| Flag | 作用 |
|---|---|
| `VARIABLE_DESCRIPTOR_COUNT_BIT_EXT` | 运行时决定数组大小 |
| `PARTIALLY_BOUND_BIT_EXT` | 不需要填充所有 descriptor |
| `UPDATE_AFTER_BIND_BIT_EXT` | 绑定后可更新 |
| `UPDATE_UNUSED_WHILE_PENDING_BIT_EXT` | GPU 不访问时可更新 |

### 传统 vs Bindless 对比

| 阶段 | 传统方式 | Bindless |
|---|---|---|
| Layout | 固定数量 binding | 大数组 + BindingFlags |
| Allocate | 固定大小 | 运行时指定数组大小 |
| Write | 一次性填充，之后不可改 | 绑定后可动态更新，`dstArrayElement` 定位 |
| Bind | 每个 draw 可能重新绑定 | 只绑定一次，着色器动态索引 |

### Bindless Write 模式（流式更新）

```cpp
vkCmdBindDescriptorSets(cmd, ..., &descriptor_set, ...);  // 绑定一次
for (i = 0; i < NumTextures; i++) {
    write.dstArrayElement = descriptor_offset;  // 写入数组位置
    vkUpdateDescriptorSets(device, 1, &write, 0, nullptr);  // 绑定后仍可更新
    vkCmdDraw(cmd, ...);
    descriptor_offset = (descriptor_offset + 1) % RingBufferSize;
}
```

着色器端使用 `nonuniformEXT` 声明动态索引：

```glsl
#extension GL_EXT_nonuniform_qualifier : enable
layout(set = 0, binding = 0) uniform texture2D textures[];
layout(push_constant) uniform PC { uint texture_index; };
vec4 color = texture(nonuniformEXT(sampler2D(textures[texture_index], Sampler)), uv);
```

---

## Uniform vs Non-uniform Indexing

### GPU 执行模型

GPU 以 SIMD 方式执行 Warp/Wavefront（32/64 线程），所有线程同时执行相同指令。两种索引模式区别：

| 概念 | 定义 |
|---|---|
| **Uniform Indexing** | 索引值在整个 draw call 中对所有线程相同 |
| **Non-uniform Indexing** | 索引值在不同线程间可能不同 |

### Uniform Indexing

索引来自 push constant 或 uniform — 整个 draw call 统一：

```glsl
layout(push_constant) uniform PC { uint texture_index; };
// ✅ 所有线程的 texture_index 相同
vec4 color = texture(sampler2D(Textures[pc.texture_index], Sampler), uv);
```

### Non-uniform Indexing

索引来自 varying 或 Shader Storage — 不同线程可能不同：

```glsl
layout(location = 0) flat in int texture_index;
void main() {
    // ⚠️ 需要 nonuniformEXT 声明
    vec4 color = texture(nonuniformEXT(sampler2D(Textures[texture_index], Sampler)), uv);
}
```

### 硬件差异

| 模式 | GPU 优化 | 限制 |
|---|---|---|
| Uniform | 绑定验证只需一次，资源访问可合并，缓存友好 | 无 |
| Non-uniform | 可能需要逐线程验证，无法合并访问 | 某些硬件可能不支持或不高效 |

Vulkan 1.0 支持 Uniform Indexing（`shaderSampledImageArrayDynamicIndexing`），Non-uniform 需要 Vulkan 1.2 / `VK_EXT_descriptor_indexing`（`shaderSampledImageArrayNonUniformIndexing`）。

---

## Descriptor Update Template vs Descriptor Indexing

两者正交，解决不同层面问题：

| 特性 | Descriptor Update Template | Descriptor Indexing |
|---|---|---|
| 解决问题 | 如何高效更新 | 如何动态选择 |
| 作用时机 | `vkUpdateDescriptorSetWithTemplate` | Shader 执行时 |
| 核心能力 | 减少 CPU 更新开销 | GPU 运行时动态索引 |

**不能互相替代**：
- Template 不解决 GPU 端动态选择问题
- Indexing 不解决 CPU 端批量更新效率问题

组合使用效果最佳：Indexing 创建 Bindless Descriptor Set，Template 高效更新大数组。

---

## Uniform Buffer vs Storage Buffer

### Dynamic Buffer（Dynamic Offset）

创建 descriptor 时可指定为 Dynamic 类型。绑定时通过 offset 控制实际访问位置，从而用单个 descriptor set 驱动多个对象：

```cpp
// 大 buffer 存储多个对象的 uniform 数据
VkDescriptorBufferInfo bufferInfos[MAX_OBJECTS];
for (int i = 0; i < MAX_OBJECTS; i++) {
    bufferInfos[i].buffer = bigUniformBuffer;
    bufferInfos[i].offset = i * OBJECT_STRIDE;  // 每个对象的偏移
    bufferInfos[i].range = OBJECT_STRIDE;
}

// 绑定时指定 dynamic offset
vkCmdBindDescriptorSets(cmd, ..., &descriptor_set, 1, &dynamicOffset);
```

适合 Uniform buffer 的多对象 draw。但 Storage buffer 推荐用 device-address。

> **现代做法**：Instanced Draw + Indirect Draw + Bindless 更灵活，由 GPU 驱动渲染，Dynamic descriptor 偏老。

### Device Address

Storage buffer 建议通过 `vkGetBufferDeviceAddress` 获取 GPU 地址，在 shader 中直接访问，而非通过 dynamic offset：

```glsl
layout(buffer_reference, std430) buffer MyStorageBuffer {
    // ...
};

// 通过 device address 访问
MyStorageBuffer buffer = MyStorageBuffer(deviceAddress);
```

---

## Descriptor Pool 溢出

描述符池不足是一个验证层无法捕获的问题的典型例子：自 Vulkan 1.1 起，如果池的大小不够， `vkAllocateDescriptorSets` 可能会以错误代码 `VK_ERROR_POOL_OUT_OF_MEMORY` 失败，但驱动程序也可能尝试内部解决这个问题。这意味着有时（取决于硬件、池大小和分配大小），驱动程序会允许我们进行超出描述符池限制的分配。其他时候， `vkAllocateDescriptorSets` 会失败并返回 `VK_ERROR_POOL_OUT_OF_MEMORY` 。如果分配在某些机器上成功，但在其他机器上失败，这可能会特别令人沮丧。

最佳实践：分配数量不超过 `descriptorCount`。开启 Best Practice Validation 可检测此问题。

## 相关主题

- [[Vulkan/Ray-Tracing|Ray-Tracing]] — 光线追踪天然依赖 Non-uniform Indexing
- [[Vulkan/Extension|Extension]] — 扩展机制与版本演进
