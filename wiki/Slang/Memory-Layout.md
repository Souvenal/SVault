# Memory Layout

Slang 原生支持 scalar 布局：

## 使用方式

### 方式1: 全局指定
```slang
layout(scalar) struct Data {
    float3 a;
    float b;
}
```

### 方式2: 在 buffer 声明时指定
```slang
[[vk::binding(0, 0)]]
StructuredBuffer<Data, scalar> buffer;
```

### 方式3: 常量缓冲区
```slang
[[vk::binding(0, 0)]]
ConstantBuffer<Data, scalar> cb;
```

## 编译命令

```shell
slangc -target spirv -stage compute shader.slang
```

## 相关主题

- [[Shader-Languages/Memory-Layout]] — 完整内存布局规则与跨语言对比