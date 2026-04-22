# Shader

Unity Shader 通用技术与语法。

---

## Unity Shader Pragmas

### DXC 编译器

`#pragma use_dxc [api]` 强制使用 DXC 编译器编译该 Shader，而非 Unity 默认的 FXC：

```hlsl
#pragma use_dxc
#pragma target 4.5
#pragma require wavebasic
```

**注意**：`d3d11` 在 Unity 中指 DX11/DX12，而非仅 DX11。

### 图形 API 限制

`#pragma only_renderers api` 只为指定图形 API 编译：

```hlsl
#pragma only_renderers d3d11
```

### Wave Operations

Wave 操作允许同一个 WARP 内的线程直接互相读取/操作彼此的数据，无需共享内存和 barrier 同步，属于硬件级指令：

```hlsl
#pragma require wavebasic
```

启用后可在 Shader 中使用 WARP 内线程通信指令。确保目标平台支持 WARP。

### 原生 Half 精度

`#pragma require Native16Bit` 启用原生 16 位浮点支持：

```hlsl
#pragma require Native16Bit
```

编译到支持 16 位浮点运算的硬件时可获得性能提升。
