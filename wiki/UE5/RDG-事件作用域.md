# RDG 事件作用域

给 `GraphBuilder` 添加 GPU 调试标记，用于性能分析和渲染调试。

---

## RDG_EVENT_SCOPE

```cpp
RDG_EVENT_SCOPE(GraphBuilder, "MyCustomPass");
```

在 RenderDoc、PIX 等 GPU 分析器中显示标记，方便定位渲染阶段。

---

## RDG_EVENT_CONDITIONAL_SCOPE

条件版本，根据运行时条件决定是否添加标记。

```cpp
RDG_EVENT_CONDITIONAL_SCOPE(GraphBuilder, bEnableDebug, "ConditionalPass");
```
