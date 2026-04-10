# FSceneViewExtensionBase

核心能力是劫持渲染流程——订阅管线的特定 Pass，在其前后插入回调，添加自定义渲染逻辑。

---

## ISceneViewExtension 核心方法

| 方法 | 何时调用 | 典型用途 |
|------|----------|----------|
| `SetupViewFamily` | 视图家族创建时 | 初始化 |
| `BeginRenderViewFamily` | 开始渲染帧时 | 模型就绪检查 |
| `PreRenderViewFamily_RenderThread` | Render Thread，每帧 | 准备 GPU 资源 |
| `SubscribeToPostProcessingPass` | 注册回调 | 订阅后处理 Pass |
| `IsActiveThisFrame_Internal` | 每帧询问 | 开关控制 |

核心是 `SubscribeToPostProcessingPass`，定义好的后处理 Pass：

- SSRInput
- MotionBlur
- ToneMap
- FXAA
- VisualizeDepthOfField
- MAX

在这些 Pass 前后可添加自定义回调。

---

## ViewFamily vs View

```cpp
// ViewFamily 层级：整个渲染家族（所有玩家/视图共享）
void PreRenderViewFamily_RenderThread(
    FRHICommandListImmediate& RHICmdList, 
    FSceneViewFamily& InViewFamily)
{
    // 检查所有 View 是否有效
    // 准备 RDG builder，准备全局资源
    // 启动渲染流程
}

// View 层级：单个摄像机视角
void PreRenderView_RenderThread(
    FRHICommandListImmediate& RHICmdList, 
    FSceneView& InView)
{
    // 获取该 View 的 viewport 大小
    // 设置 view-specific 的 shader 参数
}
```

| | PreRenderViewFamily | PreRenderView |
|---|---|---|
| 调用次数/帧 | 1 次（无论几个玩家） | 每个玩家/视图各 1 次 |
| 分屏时 | 只执行一次（全局） | 执行多次（每个分屏） |
| 可访问 | ViewFamily 全局信息 | 单个 View 的 CameraInfo |

单人游戏两者效果相同。分屏/多视图时，`PreRenderView` 会每个玩家单独触发一次。
