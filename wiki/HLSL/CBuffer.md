# CBuffer

**cbuffer** 是 HLSL（High-Level Shading Language，DirectX 着色器语言）中用来声明 **常量缓冲区（Constant Buffer）** 的关键字。它本质上是一种特殊的缓冲区类型，用于将一组 **常量数据**（如矩阵、向量、光源参数、材质属性等）从 CPU 高效地传递到 GPU 着色器中。

## cbuffer 基础

声明 cbuffer 的基本语法如下：

```hlsl
cbuffer MySceneConstants : register(b0)
{
    float4x4    g_WorldViewProj;    // 矩阵
    float3      g_LightDir;         // 光方向（注意：float3 可能有 padding）
    float       g_Time;             // 时间
    int         g_SomeFlag;         // 开关
};

float4 PSMain(float4 pos : SV_POSITION) : SV_Target
{
    // 直接用变量名，无需前缀
    float4 worldPos = mul(pos, g_WorldViewProj);
    float lit = saturate(dot(normalize(g_LightDir), float3(0,1,0)));
    return float4(lit, lit, lit, 1) * (g_SomeFlag > 0 ? 1.5 : 1.0);
}
```

使用 **ConstantBuffer\<T\>** 模板形式的写法：

```hlsl
// 声明 ConstantBuffer
ConstantBuffer<SceneConstants> MySceneCB : register(b0);

float4 PSMain(float4 pos : SV_POSITION) : SV_Target
{
    // 必须加前缀 MySceneCB.
    float4 worldPos = mul(pos, MySceneCB.WorldViewProj);
    float lit = saturate(dot(normalize(MySceneCB.LightDir.xyz), float3(0,1,0)));
    return float4(lit, lit, lit, 1) * (MySceneCB.SomeFlag > 0 ? 1.5 : 1.0);
}
```

## cbuffer vs ConstantBuffer\<T\> 对比表

| 方面                    | 传统 cbuffer                                   | ConstantBuffer\<T\> (模板形式)                                                     | 实际影响 / 推荐场景                                       |
| --------------------- | -------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------- |
| **语法示例**              | cbuffer MyCB { float4x4 m; }                 | ConstantBuffer\<MyStruct\> MyCB : register(b0);                                         |                                                   |
| **变量访问方式**            | 变量直接成为全局变量（e.g. 直接用 `worldViewProj`）         | 必须通过实例访问（e.g. `MyCB.worldViewProj`）                                          | ConstantBuffer\<T\> 命名空间更干净，避免全局污染                  |
| **索引/数组支持**           | 不支持数组或索引（旧式 cbuffer 不可索引）                    | 支持数组和索引（e.g. `ConstantBuffer<MyConstants> MyCBs[4] : register(b0, space1);`） | ConstantBuffer\<T\> 支持动态/多实例绑定（SM 5.1+ 引入）          |
| **引入时间**              | Shader Model 4.0+（DirectX 10 时代）             | Shader Model 5.1+（DirectX 12 重点推广）                                           | ConstantBuffer\<T\> 是现代 DX12 推荐写法                   |
| **向后兼容性**             | 完全兼容旧项目和旧编译器（fxc）                            | 需要 DXC 或 SM 5.1+ 支持；旧式 cbuffer 仍兼容但不可索引                                      | Unity/现代项目常用 ConstantBuffer\<T\>                    |
| **布局与打包**             | 两者相同：16 字节 vector 对齐、256 字节绑定对齐、padding 规则一致 | 相同（布局规则由 HLSL 决定，与 struct 顺序无关）                                              | 无区别，但 ConstantBuffer\<T\> 更容易与 C++ struct 对齐        |
| **Compute Shader 支持** | 支持，但 struct 在 cbuffer 中常有限制（尤其 DXC）          | 支持更好（模板形式更清晰，Compute 中 struct 兼容性���强，但仍推荐扁平或 StructuredBuffer）                | Compute 里两者都可能有 struct 问题，但 ConstantBuffer\<T\> 更现代 |
| **反射/调试**             | 反射时变量是全局的，易混淆                                | 反射更结构化（字段属于特定 buffer）                                                        | ConstantBuffer\<T\> 在工具（如 Nsight/PIX）中显示更清晰         |
| **Unity 中的使用**        | Unity 常用 `CBUFFER_START` 宏包裹传统 cbuffer       | 支持（URP/HDRP 项目常见，尤其 SM6+）                                                    | Unity 推荐用宏 + ConstantBuffer\<T\> 跨平台                |

## cbuffer 主要特性

### 1. 优化读取性能（低延迟访问）

硬件层面有专用的**常量缓存**（constant cache）或寄存器来存储这些数据。当着色器所有线程（warp/wavefront）读取**相同地址**时，性能极高（广播式读取，几乎零开销）。适合"uniform"数据：对所有像素/顶点/线程都相同的值（如 MVP 矩阵、光源、全局时间）。

### 2. 只读（Read-Only）

在着色器中**不能写入** cbuffer 数据（写入会编译错误）。只能从 CPU 侧更新，然后绑定到管线。

### 3. 大小与限制

每个 cbuffer 最多容纳 **4096 个 vector**（每个 vector 4×32-bit = 16 字节），总大小约 **64KB**（4096 × 16）。绑定槽位：DX11/DX12 每个管线阶段最多绑定 **14 个** cbuffer（b0 ~ b13，b14/b15 预留）。**必须 256 字节对齐**（绑定时资源起始地址需是 256 的倍数，否则运行时错误）。

### 4. 更新方式高效

允许把多个常量打包成一个缓冲区，一次更新全部，而不是逐个 SetXXX（旧时代做法）。减少 CPU 到 GPU 的 API 调用开销和带宽。

### 5. 布局与打包规则（Packing Rules）

HLSL 有严格的**16 字节对齐**规则（vector 必须从 16 字节边界开始）。常见陷阱：

- `float3` 可能被填充成 `float4`（浪费 4 字节）。
- `float` 后面接 `float3` 可能导致错位。

**推荐做法**（尤其 Unity 多平台）：

- 用 `float4`、`float4x4` 代替 `float3`、`float3x3`。
- 按**从大到小**顺序声明变量（float4x4 → float4 → float2 → float）。
- 避免在 cbuffer 里放 struct（某些场景如 Compute Shader + DXC 会直接报错）。

关于布局和打包的更多细节，参阅 [[Shader-Layout]]。

### 6. 与 StructuredBuffer 的对比

| 特性                | cbuffer (Constant Buffer)         | StructuredBuffer / Buffer |
| ----------------- | --------------------------------- | ------------------------- |
| 数据类型              | 任意混合（float、int、matrix 等）          | 统一结构体数组                   |
| 大小限制              | ~64KB                             | 通常更大（GB 级）                |
| 读取性能              | 极快（broadcast + 专用缓存）              | 较慢（texture-like 缓存）       |
| 适合场景              | uniform、全局常量、少量参数                 | 大数组、per-instance 数据、可索引   |
| 更新频率              | 高频更新（per-frame）                   | 中低频（数据量大时）                |
| 对齐要求              | 256 字节对齐 + 内部 16 字节规则             | 元素对齐（通常 4/16 字节）          |
| Compute Shader 支持 | 有限（Unity 中 Compute 里 struct 常不支持） | 优秀（推荐用于 Compute）          |

### 7. Unity 中的特殊性

- Unity 用宏 `CBUFFER_START(name)` / `CBUFFER_END` 包裹 cbuffer（兼容多 API）。
- Compute Shader 中 cbuffer 支持较弱：**不能放 struct**（DXC 会报 "structs in constant buffers not supported for compute shaders"）。
- 多平台（DX/Vulkan/Metal/OpenGL）下布局必须一致，否则数据错位 → 强制用 float4 等避免 padding 差异。

## cbuffer 与 uniform 的关系

- **功能上完全等价**：都是高效传递"uniform-like"常量数据的缓冲区，硬件优化方向一致（低延迟、广播读取）。
- **本质区别**在于**API 生态和布局规则**：
    - cbuffer 是 **DirectX/HLSL** 的原生叫法，更注重寄存器绑定和 packing 优化。
    - uniform buffer / UBO 是 **OpenGL/Vulkan/GLSL** 的叫法，更强调 descriptor 绑定和灵活性。

更多对比信息参阅 [[UniformBuffer]]。