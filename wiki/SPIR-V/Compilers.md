# SPIR-V Compilers

着色器编译器工具链：HLSLcc、D3DCompiler、DXC。

## HLSLcc

HLSLcc (HLSL Cross Compiler) 是一个字节码转换工具，不是编译器。

**输入**: DXBC 字节码 (由 D3DCompiler 编译产生)
**输出**: 其他着色器语言

```c
// hlslcc.h:24-42 - 支持的目标语言
typedef enum {
    LANG_ES_100, LANG_ES_300, LANG_ES_310,     // OpenGL ES
    LANG_120, LANG_130, ..., LANG_450,          // Desktop OpenGL/GLSL
    LANG_METAL,                                  // Apple Metal
} GLLang;
```

**用途**: Unity 使用 HLSLcc 将 D3D 编译的字节码转换为跨平台格式：
- OpenGL/GLES: DXBC → GLSL
- Metal: DXBC → MSL (Metal Shading Language)

## D3DCompiler vs DXC

不是同一个东西！

### D3DCompiler (传统编译器)

```c
// D3DCompilerWrapper.h:8
#define kD3DCompilerDLL "D3DCompiler_47.dll"
```

- 微软传统 HLSL 编译器
- 输出: DXBC (DirectX Bytecode)
- 限制: 不支持新特性 (Wave operations, Ray Tracing, 等)

### DXC (DirectX Compiler)

```c
// CompilerDXC.cpp:217-219
static IDxcCompiler3* g_pDxCompiler3;
static IDxcUtils* g_pDxcUtils;
```

- 微软新一代编译器 (基于 LLVM/Clang)
- 输出: DXIL (DirectX Intermediate Language) 或 SPIR-V
- 支持: Wave ops, Ray Tracing, Mesh Shaders, 等

## 完整编译流程

```
                    ┌─────────────┐
                    │  HLSL Code  │
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
    D3DCompiler         DXC              DXC
         │                 │                 │
         ▼                 ▼                 ▼
      DXBC              DXIL            SPIR-V
         │                 │                 │
    HLSLcc 转换        Direct3D 12       Vulkan
         │
    ┌────┴────┬─────────┐
    ▼         ▼         ▼
  GLSL     Metal     GLES
 OpenGL    Apple    Mobile
```

## GLSL 编译

```shell
glslc foo.vert -o foo.vert.spv
```

## spirv-dis

SPIR-V 反汇编工具，可查看 push constant 等的对齐情况：

```shell
spirv-dis shader.spv
spirv-dis shader.spv | rg push_constant
```

## HLSL → SPIR-V 布局控制

HLSL 在 Vulkan 上运行时，通过 DXC 编译到 SPIR-V。HLSL 本身没有直接的 scalar 关键字，但通过 DXC 编译器编译到 SPIR-V 时可以启用：

```shell
dxc -spirv -fspv-target-env=vulkan1.1 -fvk-use-scalar-layout shader.hlsl
```

- `-fvk-use-scalar-layout` 标志会让所有 constant buffer 和 structured buffer 使用标量布局。详细规则参阅 [[Shader-Languages/Memory-Layout]]。
