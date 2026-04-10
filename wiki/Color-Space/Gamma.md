# Color Space

色彩空间与 Gamma 校正知识。

---

## Gamma 校正

显示器在进行成像时是在 Gamma 空间的，也就是说 0.5 的像素值实际显示为 $0.5^{gamma}$。

大多数普通用户是没有校准过的显示器、也不知道 Gamma 校正的概念，所以大多数视觉素材都是已经预先矫正过的。比如 JPEG 标准中，图像预先进行了 2.2 的伽马校正，所以 JPEG 图像不是线性图像，而着色器通常假定使用线性输入，所以着色器不应该使用 JPEG 作为纹理贴图。

因为对图像进行了 Gamma 校正，所以图像在暗部区域的色彩分辨率更高，这符合人眼对暗部细微明暗变化更敏感的现象。但是这种格式在用于渲染或者合成前，都需要进行处理。

Mipmap 有两种相关的问题：
1. 创建 mipmap 时，在计算低层级的像素值时下采样是线性运算，不能在 gamma 空间内计算。解决方法：先将纹理转换到线性空间，下采样后转换回 gamma 空间。
2. GPU 采样纹理时混合两个 mipmap 层级也是线性运算，不能在 gamma 空间做。解决方法：让 GPU 使用硬件支持的 sRGB 格式读取，GPU 会自动进行相关转换。

自动的 sRGB 校正通过硬件实现，无需额外指令开销，优于在着色器中手动计算；着色器中的手动校正也是发生在滤波后的，滤波过程仍会产生错误；在加载纹理前将其预先变换到线性空间也有一定缺陷，会造成一定的 8 位像素精度损失。指定 sRGB 帧缓冲也会在最终成像时自动完成 gamma 校正，不需要在着色器中处理。

手动转换示例：
```glsl
float3 diffuseCol = pow(f3tex2D(diffTex, texCoord), 2.2);
// Or (cheaper, but assuming gamma of 2.0 rather than 2.2)
float3 diffuseCol = f3tex2D(diffTex, texCoord);
diffuseCol = diffuseCol * diffuseCol;
```

渲染计算的光照也是在线性空间的，直接在显示器上成像整体会偏暗，并且会出现从亮到暗的过渡会更快、角落更暗等问题。

有时会同时出现一些错误，比如说使用非线性颜色空间的纹理进行着色，且没有对最终图像进行 Gamma 校正，这两种校正方向相反，而最后的结果既错又难察觉。

Alpha 通道、法线贴图、置换贴图等通常都是在线性空间的，不需要校正。

如果要对图像做后处理，Gamma 校正应该作为后处理的最后一步，不要在中间结果进行校正。

---

ref: https://developer.nvidia.com/gpugems/gpugems3/part-iv-image-effects/chapter-24-importance-being-linear
