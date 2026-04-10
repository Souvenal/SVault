# PBR

基于物理的渲染（Physically Based Rendering）理论基础。

---

## BRDF 能量守恒

$$
\forall \mathbf{l}, \int_{\Omega} f(\mathbf{l}, \mathbf{v}) (\mathbf{n}\cdot\mathbf{v})\mathrm{d}\omega_{o} \leq 1.
$$

## 微表面模型

分母上不能保证修正因子为正，为了避免出现负数或者零，通常在 clamp 后加上一个小正数。

微表面模型中几何项影响是否能量守恒，需要考虑 masking。

微表面模型的缺陷：无法模拟波动光学的一些性质，以及微表面的高度和法线没有相关性（在掠射角度看物体的某些现象无法模拟），以及只模拟了单跳反射。

常见 D、F、G 函数的近似实现，令 $\alpha = {roughness}^{2}$：

$$
NDF_{GGXTR}(n, h, \alpha)=\frac{\alpha^{2}}{\pi ((n\cdot h)^{2} (\alpha^{2} - 1) + 1)^{2}},
$$

$$
G_{SchlickGGX}(n,v,k)=\frac{n\cdot v}{(n\cdot v)(1-k)+k},
$$
其中 $k$ 是粗糙度的函数，对于直接光照和 IBL 光照有不同定义：
$$
k_{direct}=\frac{(\alpha+1)^{2}}{8},k_{IBL}=\frac{\alpha^2}{2}
$$

为了有效估算几何项，需要考虑几何阴影和几何遮蔽，可以使用 Smith's method 来把两者纳入其中：
$$
G(n,v,l,k)=G_{sub}(n,v,k)G_{sub}(n,l,k)
$$

在 UE4 中，使用的几何项是 Schlick 对 Smith method 的近似，取 $k=\frac{\alpha}{2}$

## Fresnel and Schlick Approximation

$$
F_{Schlick}(c_{spec}, l, n)=c_{spec}+(1-c_{spec})(1-(l\cdot n))^5
$$

$c_{spec}$ 是不同材质入射角为 0 时的 Fresnel 值，不同材质的 Fresnel 值在 0-45 度基本是不变的，这是近似的一个依据。

金属的 $F(0)$ 通常都比较大，非金属的 $F(0)$ 则普遍较小。Schlick 近似适用于非金属。

---

ref: https://graphicrants.blogspot.com/2013/08/specular-brdf-reference.html
