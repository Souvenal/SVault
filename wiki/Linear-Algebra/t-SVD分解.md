# t-SVD 分解

张量分解方法，将张量分解为核心张量与因子矩阵的乘积。

---

## Kronecker 积性质

设 $\mathbf{X} \in \mathbb{R}^{m \times m}$, $\mathbf{Y} \in \mathbb{R}^{n \times n}$。

| 性质 | 公式 |
|------|------|
| 结合律 | $(X \otimes Y) \otimes Z = X \otimes (Y \otimes Z)$ |
| 分配律 | $X \otimes (Y + Z) = X \otimes Y + X \otimes Z$ |
| 转置 | $(X \otimes Y)^\top = X^\top \otimes Y^\top$ |
| 矩阵乘法 | $(X \otimes Y)(U \otimes V) = (XU) \otimes (YV)$ |
| 逆 | 若 $X$, $Y$ 非奇异，$(X \otimes Y)^{-1} = X^{-1} \otimes Y^{-1}$ |
| 迹 | $\mathrm{tr}(X \otimes Y) = \mathrm{tr}(X) \cdot \mathrm{tr}(Y)$ |
| F-范数 | $\|X \otimes Y\|_F = \|X\|_F \cdot \|Y\|_F$ |
| $\ell_2$ 范数 | $\|X \otimes Y\|_2 = \|X\|_2 \cdot \|Y\|_2$ |
| 行列式 | $\det(X \otimes Y) = \det(X)^n \cdot \det(Y)^m$ |
| 秩 | $\mathrm{rank}(X \otimes Y) = \mathrm{rank}(X) \cdot \mathrm{rank}(Y)$ |

---

## 参考

ref: https://zhuanlan.zhihu.com/p/662877254
