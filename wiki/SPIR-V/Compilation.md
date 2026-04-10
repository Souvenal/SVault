# Shader Compilation

GLSL 编译为 SPIR-V 字节码的通用流程。

## glslc

命令行编译器，将 GLSL 编译为 SPIR-V：

```bash
glslc shader.vert -o shader.vert.spv
# 人类可读格式输出
glslc shader.frag -o - -Os --output-format=assembly
```

## libshaderc

将 shaderc 库作为 lib 集成，可在运行时编译 GLSL 为 SPIR-V。

## Specialization Info

通过 `pSpecializationInfo` 指定 shader 常量，驱动可针对常量进行优化：

```cpp
specInfo.mapEntryCount = 1;
specInfo.pMapEntries = &mapEntry;
specInfo.dataSize = sizeof(value);
specInfo.pData = &value;
```

## Entry Point

可在单个 Shader Module 中定义多个 entry point，fragment shader 中区分不同行为。

## 运行时编译优势

集成 SPIR-V 编译器作为库，可动态生成 shader（如 procedural 效果）。

## 相关

[[SPIR-V/Compilers]] — HLSLcc、DXC 等编译器对比
