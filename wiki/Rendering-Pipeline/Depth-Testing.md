# Depth Testing

深度测试但不写入深度缓冲区，适用于需要与场景混合但不遮挡后方物体的渲染场景。

---

## 透明物体渲染

```cpp
// 先渲染不透明物体（写入深度）
pipeline.enableDepthWrite(true);
pipeline.enableDepthTest(true, CompareOp::Less);
renderOpaqueObjects();

// 再渲染透明物体（只测试，不写入）
pipeline.enableDepthWrite(false);
pipeline.enableDepthTest(true, CompareOp::Less);
renderTransparentObjects();  // 从远到近排序
```

透明物体需要与后方物体混合。写入深度会阻挡后方透明物体，但深度测试仍能避免被不透明物体遮挡的部分被渲染。

## 多层透明效果

场景：多层玻璃、烟雾。按距离从远到近排序渲染，每层都能看到后方层。

```cpp
for (auto& layer : sortedLayers(descending_by_distance)) {
    pipeline.enableDepthWrite(false);
    pipeline.enableDepthTest(true, CompareOp::Less);
    renderLayer(layer);
}
```

## 粒子系统

```cpp
pipeline.enableDepthWrite(false);
pipeline.enableDepthTest(true, CompareOp::Less);
pipeline.enableBlending(BlendingMode::Additive);
renderParticles();
```

粒子需要深度测试避免渲染在固体物体前面，但不写深度允许粒子之间叠加。

典型场景：火焰、爆炸特效、魔法光效、尘土。

## UI 元素的深度交互

```cpp
// 角色头顶的血条
pipeline.enableDepthWrite(false);
pipeline.enableDepthTest(true, CompareOp::Less);
renderHealthBars();
```

需要被墙壁遮挡（深度测试），但不影响其他 UI 元素渲染（不写深度）。

## 软粒子技术

避免粒子与场景交界处出现硬边缘。根据粒子深度与场景深度差异进行淡化。

```glsl
// fragment shader
float sceneDepth = texture(depthMap, screenUV).r;
float particleDepth = gl_FragCoord.z;
float fade = smoothstep(0.0, fadeDistance, abs(sceneDepth - particleDepth));
color.a *= fade;
```

## 体积渲染

体积云、雾效。多层采样，每层贡献颜色但不写深度。

```cpp
for (int layer = 0; layer < volumeLayers; ++layer) {
    pipeline.enableDepthWrite(false);
    pipeline.enableDepthTest(true, CompareOp::Less);
    renderVolumeLayer(layer);
}
```

## X-Ray 效果

透过墙壁看到物体。使用 `CompareOp::Greater` 只渲染被遮挡的部分。

```cpp
pipeline.enableDepthWrite(false);
pipeline.enableDepthTest(true, CompareOp::Greater);
pipeline.enableBlending(BlendingMode::Alpha);
renderXRayObjects();
```

---

## 渲染顺序

```
1. 深度预处理（可选）
2. 不透明物体（利用早期 Z 测试）
3. 天空盒
4. 透明物体（远到近）
5. 粒子效果
6. UI 覆盖层
```
