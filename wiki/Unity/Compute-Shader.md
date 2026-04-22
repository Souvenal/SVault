# Compute Shader

Unity 中 Compute Shader 的编写与调用方式。

---

## HLSL 入口声明

使用 `#pragma kernel` 声明函数入口，可指定多个入口：

```hlsl
#pragma kernel funcA funcB ...

[numthreads(8, 8, 1)]
void funcA(uint3 id : SV_DispatchThreadID)
{
    // ...
}

[numthreads(8, 8, 1)]
void funcB(uint3 id : SV_DispatchThreadID)
{
    // ...
}
```

---

## C# 调用

通过 `ComputeShader` 类进行调用：

```csharp
public class Bar : MonoBehaviour
{
    public ComputeShader fooCS = null;  // Inspector 拖拽赋值
    
    void OnPreRender()
    {
        int kernelIndex = fooCS.FindKernel("funcA");
        
        // 获取线程组大小
        uint threadGroupSizeX, threadGroupSizeY, threadGroupSizeZ;
        fooCS.GetKernelThreadGroupSizes(kernelIndex, 
            out threadGroupSizeX, out threadGroupSizeY, out threadGroupSizeZ);

        CommandBuffer cmdBuffer = new CommandBuffer();
        cmdBuffer.DispatchCompute(fooCS, kernelIndex, 1, 1, 1);
    }
}
```

---

## 资源绑定

通过 `CommandBuffer` 设置各种参数：

```csharp
CommandBuffer cmdBuffer = new CommandBuffer();
cmdBuffer.SetComputeVectorParam(cs, "g_DepthToViewParams", DepthToViewParams);
cmdBuffer.SetComputeIntParam(cs, "g_FrameIndex", frameIndex);
cmdBuffer.SetComputeVectorParam(cs, "g_LightDir", dirLight.transform.forward);
cmdBuffer.SetComputeFloatParam(cs, "g_ShadowSpread", shadowSpread * 0.1f);
cmdBuffer.SetRayTracingAccelerationStructure(cs, kernelIndex, "g_AccelStruct", rtas);
cmdBuffer.SetComputeTextureParam(cs, kernelIndex, "g_Output", shadowMapTexture);
```

---

## Shader Variables

Unity HDRP 在 `Packages/com.unity.render-pipelines.high-definition/Runtime/ShaderLibrary/ShaderVariables.hlsl` 中定义常用变量。

详见 [[Shader]] — Shader 通用语法：Pragmas、DXC 编译器、Wave Operations、Native16Bit。


