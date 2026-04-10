# Memory Layouts

SPIR-V / Vulkan 着色器内存布局：std140、std430、scalar。

## std140 Layout

std140 是 OpenGL 4.2+ 引入的 Uniform Buffer Object (UBO) 标准布局。若未指定 uniform 的布局，则默认为 std140。

### 核心规则

| 类型 | 对齐要求 (Alignment) | 大小 |
|:---|:---|:---|
| `float`, `int`, `uint`, `bool` | 4 字节 | 4 字节 |
| `vec2`, `ivec2` 等 | 8 字节 | 8 字节 |
| `vec3`, `vec4`, `ivec3`, `ivec4` 等 | 16 字节 | 16 字节 |
| `mat4` | 16 字节 (按列存储，每列 vec4) | 64 字节 |
| `mat3` | 16 字节 (每列 vec4 填充) | 48 字节 |
| 数组 | 每个元素按 16 字节对齐 | n × 16 字节 |
| 结构体 | 按成员最大对齐要求，整体大小补齐到对齐倍数 | - |

### 关键特性

- vec3 也要占 16 字节（后面填充 4 字节）
- 数组元素强制 16 字节对齐，即使 `float` 数组，每个元素也占 16 字节
- 跨平台兼容性好，所有 GPU 厂商实现一致

```glsl
layout(std140) uniform MyUBO {
    float a;      // offset 0,  占用 4 字节 (实际占 16 due to vec3 alignment)
    vec3 b;       // offset 16, 占用 16 字节
    float c;      // offset 32, 占用 4 字节
    // 总大小: 48 字节，但 vec3 强制对齐导致前面有填充
};
```

## std430 Layout

std430 是 OpenGL 4.3+ 引入的 Shader Storage Buffer Object (SSBO) 标准布局，若未指定 buffer 的布局，则默认为 std430。

### 与 std140 的主要区别

| 特性 | std140 | std430 |
|:---|:---|:---|
| 用途 | UBO (Uniform Buffer) | SSBO (Storage Buffer) |
| 数组元素 | 16 字节对齐 | 紧凑排列 |
| 结构体 | 16 字节对齐 | 按成员最大对齐要求 |

std430 主要优化了数组的布局，相同数据，std430 比 std140 节省约 25-50% 显存。

## Scalar Layout

Scalar Layout 是 Vulkan、DirectX 12、Metal 等现代 API 引入的最紧凑布局，也是 GL_EXT_scalar_block_layout 扩展提供的布局。

### 核心思想

完全按 C 语言结构体的自然对齐方式排列，没有任何额外的对齐填充。

| 类型 | 对齐 | 大小 |
|:---|:---|:---|
| `float`, `int` | 4 字节 | 4 字节 |
| `vec2` | 8 字节 | 8 字节 |
| `vec3` | 4 字节对齐 | 12 字节 |
| `vec4` | 16 字节 | 16 字节 |
| `mat4` | 4 字节对齐 | 64 字节 (连续存储) |
| 数组 | 元素自然对齐 | n × sizeof(element) |

## 实际使用策略

不能全部使用 scalar layout 的原因：
- scalar 内存最优，但不是性能最优，紧凑排列数据可能会导致某个变量跨越 cache line。所以高频访问的 uniform 会默认使用 std140，存储大数据的 SSBO 会默认使用 std140。
- 旧 GPU 以及旧驱动的支持不足。

在引擎架构设计中，通常会在抽象层统一使用 scalar layout，并在不支持的平台做兼容处理。

分层方案：

```glsl
// 1. 关键高频数据：保持 std140/std430 对齐，牺牲空间换速度
layout(std140, set=0, binding=0) uniform CameraUBO {
    mat4 viewProj;      // 高频访问，对齐优先
    vec3 cameraPos;
    float padding;      // 手动填充到 16
};

// 2. 大数据集/低频更新：用 scalar 节省显存
layout(scalar, set=0, binding=1) readonly buffer Meshlets {
    uint vertexCount;
    uint triangleCount;
    uvec3 indices[];    // 紧密排列，节省 25% 显存
};

// 3. 顶点数据：手动对齐 + scalar 混合
struct Vertex {
    vec3 pos;      // 12 bytes
    uint padding;  // 4 bytes, 手动对齐到 16
    vec3 normal;   // 12 bytes  
    uint padding2; // 4 bytes
}; // 32 bytes，对齐且紧凑
```

| 场景 | 推荐布局 | 理由 |
|:---|:---|:---|
| UBO / 常量缓冲区 | std140 | 小数据、高频访问、最大兼容 |
| SSBO / 大数据 | std430 或 scalar | 显存敏感，访问模式可预测 |
| 顶点缓冲区 | 手动对齐 + scalar | GPU 有专用顶点加载单元，对齐要求低 |
| 计算着色器随机访问 | std430 | 避免非对齐访问惩罚 |
| 间接绘制命令 | scalar | 数据小、结构复杂、节省显存关键 |
| 骨骼/动画数据 | scalar | 大量 `mat4x3`，节省 25% 显存值得冒险 |

> 注：还是不要想当然，先 Profile 再优化

## HLSL / Slang 标量布局

### HLSL

HLSL 本身没有直接的 scalar 关键字，但通过 DXC 编译器编译到 SPIR-V 时可以启用：

```shell
dxc -spirv -fspv-target-env=vulkan1.1 -fvk-use-scalar-layout shader.hlsl
```

```hlsl
// HLSL 代码
struct Data {
    float3 a;
    float b;
};

[[vk::binding(0, 0)]]
ConstantBuffer<Data> cb;  // 使用 -fvk-use-scalar-layout 后按标量布局
```

### Slang

Slang 原生支持 scalar 布局：

```slang
// 方式1: 全局指定
layout(scalar) struct Data {
    float3 a;
    float b;
}

// 方式2: 在 buffer 声明时指定
[[vk::binding(0, 0)]]
StructuredBuffer<Data, scalar> buffer;

// 方式3: 常量缓冲区
[[vk::binding(0, 0)]]
ConstantBuffer<Data, scalar> cb;
```

```shell
slangc -target spirv -stage compute shader.slang
```

### 三语言对比总结

| 语言 | 标量布局支持方式 |
|:---|:---|
| GLSL | `layout(scalar)` 限定符 |
| HLSL | DXC 编译器标志 `-fvk-use-scalar-layout` |
| Slang | `scalar` 泛型参数或 `layout(scalar)` |

## groupshared 特殊说明

hlsl 里面 `groupshared` 变量在编译成 spv 被 vulkan 使用时，vulkan 端需要启用 `VK_KHR_workgroup_memory_explicit_layout` 拓展。
