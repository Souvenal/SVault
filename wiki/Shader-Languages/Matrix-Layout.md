# Matrix Layout

HLSL 和 Slang 的矩阵布局与 GLSL/GLM 完全相反，这是跨平台开发中最常见的坑之一。

## 默认行为对比

| 特性               | GLSL/GLM           | HLSL            | Slang (默认)         |
| :--------------- | :----------------- | :-------------- | :----------------- |
| 默认矩阵布局           | 列主序 (column-major) | 行主序 (row-major) | 列主序 (column-major) |
| 构造函数填充顺序         | 按列                 | 按行              | 按列                 |
| `m[0]` 含义        | 第一列                | 第一行             | 第一列                |
| 与 GLM 默认兼容       | ✅                  | ❌ 需转置           | ✅                  |
| 与 DirectXMath 兼容 | ❌                  | ✅               | ❌                  |

## HLSL 默认行为

```hlsl
// HLSL 默认：行主序
cbuffer Camera : register(b0) {
    float4x4 view;  // 内存按行存储：r0, r1, r2, r3
};
```

### HLSL 构造方式

```hlsl
// HLSL：构造函数按行填充（符合数学直觉）
float4x4 m = float4x4(
    1, 2, 3, 4,      // 第一行 (row 0)
    5, 6, 7, 8,      // 第二行 (row 1)
    9, 10, 11, 12,   // 第三行 (row 2)
    13, 14, 15, 16   // 第四行 (row 3)
);

// 内存布局: [1,2,3,4, 5,6,7,8, 9,10,11,12, 13,14,15,16] (行优先)
```

### HLSL 修改布局

```hlsl
// 1. 单个变量指定
column_major float4x4 view;  // 强制列主序

// 2. 整个 cbuffer 指定
cbuffer Camera {
    row_major float4x4 proj;      // 行主序（默认，可省略）
    column_major float4x4 view;   // 列主序
};

// 3. 编译器指令（全局）
#pragma pack_matrix(column_major)  // 从此处开始默认列主序
```

## Slang 行为

Slang 默认继承源语言习惯，但提供显式控制：

| 源语言模式 | 默认矩阵布局 |
|:---|:---|
| `slang` (默认) | 列主序（与 GLSL 一致） |
| `hlsl` 模式 | 行主序（与 HLSL 一致） |
| `glsl` 模式 | 列主序 |

```slang
// Slang 默认（slang 模式）
cbuffer Camera {
    float4x4 view;  // 列主序！
};

// 显式指定
row_major float4x4 proj;      // 行主序
column_major float4x4 view;   // 列主序（默认）
```

## 跨平台引擎解决方案

### 方案 1：统一为列主序（推荐）

```hlsl
// HLSL 端强制列主序
#pragma pack_matrix(column_major)

cbuffer Camera {
    float4x4 view;
    float4x4 proj;
};
```

```cpp
// C++ 端 GLM 保持默认
glm::mat4 view = ...;
// 直接上传，无需转置
```

### 方案 2：上传时转置

```cpp
// C++ 端
glm::mat4 view = ...;
// HLSL 保持行主序默认，上传时转置
context->SetMatrix(view, /* transpose */ true);
```

### 方案 3：Slang 作为抽象层

```slang
// 用 Slang 写 shader，编译到不同目标时自动处理
// Slang 默认列主序，编译到 HLSL 时可自动插入转置或 row_major 修饰
```

## 常见坑点

```cpp
// 错误：直接复制导致矩阵错乱
// C++ (GLM 列主序)
glm::mat4 m = ...;

// HLSL 默认行主序 cbuffer
cbuffer { float4x4 m; }  // 错误！会把列当行读

// 结果：矩阵被隐式转置，变换完全错误
```

```cpp
// 正确做法 1：HLSL 强制列主序
#pragma pack_matrix(column_major)
cbuffer { float4x4 m; }  // 正确匹配

// 正确做法 2：上传时转置
// C++ 端计算转置后上传
DirectX::XMMATRIX xm = DirectX::XMMatrixTranspose(GLMToDX(m));
```
