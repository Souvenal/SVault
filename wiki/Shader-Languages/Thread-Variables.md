# Thread Variables

GLSL / HLSL / Slang 线程变量对比。

## 线程变量对比表

| 含义                                | GLSL                      | HLSL                          | Slang                        |
| :-------------------------------- | :------------------------ | :---------------------------- | :--------------------------- |
| 全局线程ID (Dispatch中的线程索引)           | `gl_GlobalInvocationID`   | `SV_DispatchThreadID`         | `SV_DispatchThreadID`        |
| 工作组内线程ID (Local invocation在组内的索引) | `gl_LocalInvocationID`    | `SV_GroupThreadID`            | `SV_GroupThreadID`           |
| 工作组ID (Workgroup在grid中的索引)        | `gl_WorkGroupID`          | `SV_GroupID`                  | `SV_GroupID`                 |
| 工作组内线程索引 (扁平化的Local索引)            | `gl_LocalInvocationIndex` | `SV_GroupIndex`               | `SV_GroupIndex`              |
| 工作组数量 (Dispatch的grid维度)           | `gl_NumWorkGroups`        | 通过常量定义 (如 `uint3 numGroups`)  | 通过常量定义 (如 `uint3 numGroups`) |
| 工作组大小 (Local workgroup的维度)        | `gl_WorkGroupSize`        | 通过 `[numthreads(x,y,z)]` 定义   | 通过 `[numthreads(x,y,z)]` 定义  |
| 3D工作组大小 (常量值)                     | `gl_WorkGroupSize.x/y/z`  | `SV_GroupSize` (只读，HLSL 6.0+) | `SV_GroupSize` (只读)          |

## 补充说明

1. **工作组数量** 在 HLSL 和 Slang 中不是内置变量，通常通过常量或参数传递：

```hlsl
[numthreads(8,8,1)]
void main(uint3 dispatchThreadID : SV_DispatchThreadID,
          uint3 groupID : SV_GroupID,
          uint3 numGroups) // 通常作为参数传入
```

2. **工作组大小** 在 GLSL 中是运行时内置变量，在 HLSL/Slang 中通过属性 `[numthreads(x,y,z)]` 定义，可通过 `SV_GroupSize` 读取。

3. Slang 兼容 HLSL 语法，所以其语义系统与 HLSL 几乎完全相同。

4. **关系公式**：

```
gl_GlobalInvocationID = gl_WorkGroupID * gl_WorkGroupSize + gl_LocalInvocationID
```

## Compute Threads 设置

### GLSL

```glsl
layout(local_size_x = 16, local_size_y = 16, local_size_z = 1) in;
```

### HLSL / Slang

```hlsl
[numthreads(16, 16, 1)]
void main(...) {}
```
