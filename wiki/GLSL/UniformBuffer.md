# Uniform Buffer

## Uniform Buffer Object 基础

Uniform Buffer Object（UBO）是 GLSL（OpenGL Shading Language）中用于声明常量缓冲区的关键字，与 HLSL 中的 [[CBuffer]] 功能等价。UBO 本质上是一种特殊的缓冲区类型，用于将一组常量数据（如矩阵、向量、光源参数、材质属性等）从 CPU 高效地传递到 GPU 着色器中。

```glsl
#version 450

layout(std140, binding = 0) uniform SceneConstants {
    mat4    g_WorldViewProj;
    vec4    g_LightDir;      // float3 被填充为 float4
    float   g_Time;
    int     g_SomeFlag;
};

void main() {
    vec4 worldPos = g_WorldViewProj * vec4(a_Position, 1.0);
    float lit = clamp(dot(normalize(g_LightDir.xyz), vec3(0,1,0)), 0.0, 1.0);
    FragColor = vec4(lit, lit, lit, 1.0) * (g_SomeFlag > 0 ? 1.5 : 1.0);
}
```

## 与 HLSL cbuffer 的对比

UBO 与 cbuffer 在功能上完全等价，都是高效传递 uniform 类型常量数据的缓冲区，硬件优化方向一致（低延迟、广播读取）。两者的本质区别在于 API 生态和布局规则：

- **GLSL/UBO**：更强调 descriptor 绑定和灵活性，通过 `layout(binding = N)` 指定绑定槽位
- **HLSL/cbuffer**：更注重寄存器绑定（`register(b0)`）和 packing 优化

| 方面 | GLSL UBO | HLSL cbuffer |
|------|----------|--------------|
| 关键字 | `uniform` + `layout(binding)` | `cbuffer` |
| 绑定方式 | Descriptor Set 绑定 | `register(b0)` 寄存器绑定 |
| 布局规则 | std140 / std430 | 16 字节对齐 |
| 更新频率 | 高频更新（per-frame） | 高频更新（per-frame） |

## 布局规则

GLSL UBO 使用两种布局限定符：**std140** 和 **std430**。这两种规则决定了成员在缓冲区中的内存布局方式。

### std140 布局

std140 是默认布局规则，遵循以下对齐规则：

- 标量（`float`、`int`）：4 字节对齐
- 向量（`vec2`、`vec3`、`vec4`）：8 字节对齐（`vec3` 仍按 16 字节对齐）
- 矩阵（`mat4`）：16 字节对齐
- 数组：元素大小按上述规则计算，数组整体按最大元素对齐
- 结构体：按其成员的最大对齐值对齐

```glsl
layout(std140) uniform Example {
    float   a;          // 偏移 0，对齐 4
    vec3    b;          // 偏移 16（float 被填充到 16 字节）
    vec2    c;          // 偏移 32，对齐 8
    mat4    d;          // 偏移 48，对齐 16
};
```

### std430 布局

std430 是更紧凑的布局规则，适用于 OpenGL 4.3+ 和 Vulkan。相比 std140，主要区别在于：

- 数组和结构体不再强制 16 字节对齐
- `vec3` 和 `vec4` 一样按 8 字节对齐（在结构体内部）

```glsl
layout(std430) uniform CompactExample {
    float   a;          // 偏移 0
    vec3    b;          // 偏移 4（不再填充）
    vec2    c;          // 偏移 16
    mat4    d;          // 偏移 32
};
```

### 与 HLSL 16 字节对齐的对比

HLSL 的 cbuffer 强制 16 字节向量对齐，而 GLSL std140 在某些情况下更宽松，但在结构体内部同样会导致填充。实际项目中，建议：

- 使用 `float4`、`vec4` 代替 `float3`，避免意外填充
- 变量按从大到小顺序声明（mat4 → vec4 → vec2 → float）
- 跨平台时使用 std430 获得更紧凑的布局

更多细节请参考 [[Shader-Layout]] 主题。

## 主要特性

1. **优化读取性能**：硬件层面有专用的常量缓存，当所有线程读取相同地址时性能极高（广播式读取）
2. **只读**：着色器中不能写入 UBO 数据
3. **大小限制**：OpenGL 每个 UBO 最大约 64KB（具体实现可能更大）
4. **绑定槽位**：OpenGL 至少支持 36 个 UBO 绑定点（`GL_MAX_UNIFORM_BUFFER_BINDINGS`）

## 相关主题

- [[CBuffer]] — HLSL 常量缓冲区详解
- [[Shader-Layout]] — 着色器内存布局全面解析