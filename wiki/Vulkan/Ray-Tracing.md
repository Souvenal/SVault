# Ray-Tracing

Vulkan 光线追踪以加速结构（AS）为核心，通过 Shader Binding Table（SBT）实现光线与着色器的调度。Ray Tracing 天然依赖 Bindless — 不同光线可能 hit 完全不同的物体，需要 Non-uniform Indexing。

---

## 加速结构（Acceleration Structure）

### 架构

```
TLAS (Top Level)
    ↓
Instances (每个 instance 指向一个 BLAS)
    ↓
BLAS (Bottom Level) — 几何图元（三角形/AABB）
```

### BLAS 构建（自底向上）

```cpp
// 1. 定义几何数据
VkAccelerationStructureGeometryKHR geometry = {
    .sType = VK_STRUCTURE_TYPE_ACCELERATION_STRUCTURE_GEOMETRY_KHR,
    .geometryType = VK_GEOMETRY_TYPE_TRIANGLES_KHR,
    .geometry.triangles = {
        .vertexFormat = VK_FORMAT_R32G32B32_SFLOAT,
        .vertexData = vertexDeviceAddress,
        .vertexStride = sizeof(Vertex),
        .maxVertex = vertexCount - 1,
        .indexType = VK_INDEX_TYPE_UINT32,
        .indexData = indexDeviceAddress,
    },
    .flags = VK_GEOMETRY_OPAQUE_BIT_KHR
};

// 2. 获取构建大小
VkAccelerationStructureBuildGeometryInfoKHR buildInfo = {
    .sType = VK_STRUCTURE_TYPE_ACCELERATION_STRUCTURE_BUILD_GEOMETRY_INFO_KHR,
    .type = VK_ACCELERATION_STRUCTURE_TYPE_BOTTOM_LEVEL_KHR,
    .mode = VK_BUILD_ACCELERATION_STRUCTURE_MODE_BUILD_KHR,
    .geometryCount = 1,
    .pGeometries = &geometry,
};
VkAccelerationStructureBuildSizesInfoKHR sizeInfo;
vkGetAccelerationStructureBuildSizesKHR(device, VK_ACCELERATION_STRUCTURE_BUILD_TYPE_DEVICE_KHR,
    &buildInfo, &maxPrimitiveCounts, &sizeInfo);

// 3. 创建 AS buffer 和 AS 对象
// 4. 实际构建
buildInfo.dstAccelerationStructure = blas;
buildInfo.scratchData.deviceAddress = scratchBufferAddress;
VkAccelerationStructureBuildRangeInfoKHR rangeInfo = {
    .primitiveCount = triangleCount,
    .primitiveOffset = 0,
    .firstVertex = 0,
    .transformOffset = 0,
};
vkCmdBuildAccelerationStructuresKHR(cmd, 1, &buildInfo, &rangeInfo);
```

### Scratch Buffer

构建加速结构时，中间变量存储在 Scratch Buffer 中，大小约为 AS 本身的 1-2 倍。Vulkan 要求 Host 显式管理 Scratch Buffer，以便对 Device 内存进行复用和池化优化。

### TLAS 构建

流程与 BLAS 类似，区别在于 geometry 传入的是 instances 信息：

```cpp
VkAccelerationStructureInstanceKHR instance = {
    .transform = transformMatrix,
    .instanceCustomIndex = 0,
    .mask = 0xFF,
    .instanceShaderBindingTableRecordOffset = 0,
    .flags = VK_GEOMETRY_INSTANCE_TRIANGLE_FACING_CULL_DISABLE_BIT_KHR,
    .accelerationStructureReference = blasDeviceAddress  // 指向 BLAS
};
// geometryType = VK_GEOMETRY_TYPE_INSTANCES_KHR
```

### 关键优化点

- **构建模式**：`BUILD`（从零，慢但质量高）vs `UPDATE`（增量，快但可能降低效率）
- **压缩（Compaction）**：构建后查询紧凑大小，复制到更小 buffer
- **多线程构建**：BLAS 可并行在多个 cmd buffer/queue
- **内存复用**：Scratch Buffer 可与最终结果内存 alias
- **双层缓冲**：AS 构建延迟一帧使用，避免 stall

### 常见坑点

- `vkGetAccelerationStructureDeviceAddressKHR` 返回 GPU 虚拟地址，不是 buffer 地址
- 实例 buffer 需要 `VK_BUFFER_USAGE_ACCELERATION_STRUCTURE_BUILD_INPUT_READ_ONLY_BIT_KHR`
- 构建后需要 `VK_ACCESS_ACCELERATION_STRUCTURE_WRITE_BIT_KHR` → `VK_PIPELINE_STAGE_RAY_TRACING_SHADER_BIT_KHR` barrier

---

## Shader Group 与 Shader Binding Table

### Shader Group

管线创建时的逻辑定义，描述着色器组合方式：

```cpp
// shader_groups[0]: GENERAL → raygen shader
// shader_groups[1]: GENERAL → miss shader
// shader_groups[2]: TRIANGLES_HIT → closesthit shader
```

### Shader Binding Table（SBT）

运行时数据结构，存储各 shader group 的句柄，供 GPU 在光线追踪时查找执行。

### 完整流程

```
1. 定义 Shader Groups (管线创建前)
   └─ 驱动编译 shader stages，生成 handles（opaque 数据）

2. vkCreateRayTracingPipelinesKHR
   └─ 驱动为每个 group 生成 handle（~32 字节，包含代码地址、寄存器配置等）

3. vkGetRayTracingShaderGroupHandlesKHR
   └─ 从管线取出 handles，复制到 SBT buffer

4. vkCmdTraceRaysKHR
   └─ GPU 通过 SBT 地址找到正确 handle → 执行代码
```

### 为什么设计成两步？

Handle 是**编译产物**，不是输入参数：
- 管线创建前，handle 不存在（GPU 微代码尚未编译）
- 驱动需要知道所有 groups 才能生成正确 handles
- Handle 格式由硬件/驱动决定，不同架构可能不同

### SBT 索引公式

```
SBT_Hit_Index = instanceShaderBindingTableRecordOffset + geometryIndex × rayStride + rayOffset
```

`instanceShaderBindingTableRecordOffset` 允许同一 geometry 使用不同的 hit shaders（通过不同 instance 引用同一 BLAS）。

---

## Bindless 依赖

Ray Tracing 天然需要 Bindless。

### 为什么需要 Non-uniform Indexing？

```
Ray 0 → hit 物体 A → 需要 texture[0]
Ray 1 → hit 物体 B → 需要 texture[5]  ← 不同纹理
Ray 2 → miss → 不需要纹理
Ray 3 → hit 物体 C → 需要 texture[12]
同一 wavefront 中不同 ray 可能访问完全不同的 descriptor
```

Graphics Pipeline 中，同一 mesh 的顶点或同一 primitive 的像素通常共享材质（Uniform Indexing 足够）。Ray Tracing 中不同光线可能 hit 完全不同物体，天然 Non-uniform。

### 两层 Non-uniform

| 层面 | 机制 | 作用 |
|---|---|---|
| **Shader Selection** | SBT（Shader Binding Table） | 不同 geometry dispatch 到不同 hit shader |
| **Resource Selection** | Descriptor Indexing | 同一 hit shader 内动态索引纹理/材质 |

两者互补：
- SBT：根据物体类型选择 shader 逻辑
- Bindless：在 shader 内根据物体索引选择纹理

```glsl
// closesthit shader 中的 bindless 纹理访问
#extension GL_EXT_nonuniform_qualifier : enable
layout(binding=7, set = 0) uniform sampler2D textures[26];
layout(location = 3) flat in int imageOffset;
vec4 tex_value = textureLod(nonuniformEXT(sampler2D(textures[imageOffset], Sampler)), texcoord, 0);
```

### 相关扩展

启用 Ray Tracing 需要同时启用 `VK_EXT_descriptor_indexing`：

```cpp
add_device_extension(VK_EXT_DESCRIPTOR_INDEXING_EXTENSION_NAME);
```

---

## 相关主题

- [[Vulkan/Descriptor|Descriptor]] — Bindless Descriptor Indexing 详解
- [[Vulkan/Extension|Extension]] — VK_KHR_acceleration_structure、RayTracingPipeline、RayQuery 扩展
