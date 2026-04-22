# Coordinate Systems

Unity 坐标系规范。

---

## 世界空间

Unity 世界空间采用 Y 轴向上的左手系坐标系（左手定则：大拇指指向 Y 轴正方向，四指弯曲方向为正旋转）。

## 视图空间

从世界空间转换到视图空间时，Unity 使用右手系，矩阵行列式为负。

```
Caution: 当从世界空间转换到视图空间时，Unity 为右手系且矩阵行列式为负
```

但对于立方体贴图捕获（Reflection Probe），视图空间仍保持左手系（符合 cubemap 约定），行列式为正。

## 单位约定

光照代码假设 1 Unity 单位（1uu）等于 1 米。这对于基于物理的光照单位和平方衰减计算非常重要。

```
The lighting code assume that 1 Unity unit (1uu) == 1 meters.
This is very important regarding physically based light unit and inverse square attenuation.
```

## 坐标空间命名规范

变量名后缀表示所在坐标空间：

| 后缀 | 含义 | 说明 |
|------|------|------|
| WS | World Space | 世界空间 |
| RWS | Camera-Relative World Space | 相机平移已相减，用于提高精度 |
| VS | View Space | 视图空间 |
| OS | Object Space | 对象空间 |
| CS | Homogenous Clip Space | 齐次裁剪空间 |
| TS | Tangent Space | 切线空间 |
| TXS | Texture Space | 纹理空间 |

**示例**：`NormalWS` 表示世界空间下的法线。
